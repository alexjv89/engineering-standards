# Open the Last Org on Launch

A multi-org app whose entry point is the org list (`/orgs`) should open the org you used last when you *open* the app, and still show the list when you *navigate* to `/orgs` from inside it. That saves a tap on every launch for people who only use one org, without taking the list away.

Reference implementation: `internalops/apps/timelog` (`src/middleware.js`, `src/app/orgs/[o_id]/RememberLastOrg.jsx`).

## The Pattern

Three pieces, plus two fixes the change needs.

### 1. Remember the last org in a cookie, set in the browser

```javascript
// src/utils/clientPreferences/index.js
// The org visited last; src/middleware.js opens it when the app is opened on /orgs
export const LAST_ORG_COOKIE = '<app>.last_org';
```

```javascript
// src/app/orgs/[o_id]/RememberLastOrg.jsx
"use client";

import { useEffect } from 'react';
import { LAST_ORG_COOKIE, setPreference } from '@/utils/clientPreferences';

export default function RememberLastOrg({ o_id }) {
  useEffect(() => {
    setPreference(LAST_ORG_COOKIE, encodeURIComponent(o_id));
  }, [o_id]);
  return null;
}
```

Render `<RememberLastOrg o_id={o_id} />` in `src/app/orgs/[o_id]/layout.jsx`, so every org page counts as a visit.

- **Use a cookie, not localStorage.** The middleware reads the cookie before rendering, so the redirect happens without first flashing the org list.
- **Keep it host-only (no `Domain`), one year, `SameSite=Lax`.** Each app and each browser remembers its own org, so no database preference is needed.
- **Set it in the browser, not the middleware.** Org cards and the org switcher prefetch `/orgs/<o_id>`. Next 15 strips the `next-router-prefetch` header before middleware sees it (`FLIGHT_HEADERS` in `next/dist/server/web/adapter.js`), so the middleware can't tell a prefetch from a visit. A client effect only runs on a real render.

### 2. Tell "opened" from "navigated" with `Sec-Fetch-Site`

In `src/middleware.js`, after the auth check:

```javascript
// Opening the app (PWA launch, bookmark, typed URL, back from login) skips the org list and
// goes to the last org. Reaching /orgs from inside the app (breadcrumb, logo) keeps the list,
// as do browsers that don't send Sec-Fetch-Site. RememberLastOrg sets the cookie.
const last_org = request.cookies.get(LAST_ORG_COOKIE)?.value;
const fetch_site = request.headers.get("sec-fetch-site");
if (pathname === "/orgs" && last_org && fetch_site && fetch_site !== "same-origin") {
  return NextResponse.redirect(new URL(`/orgs/${encodeURIComponent(last_org)}`, APP_URL));
}
```

| `Sec-Fetch-Site` | Case | Result |
|---|---|---|
| `none` | PWA launch, bookmark, typed URL | redirect |
| `same-site` | back from login on the console subdomain | redirect |
| `cross-site` | link from another site | redirect |
| `same-origin` | breadcrumb, sidebar logo, other in-app links | org list |
| missing | browsers older than Safari 16.4 | org list |

A missing header keeps the list, so old browsers lose the shortcut but never lose access to the list.

### 3. Redirect to the org's entry page, change nothing else

Redirect to `/orgs/<o_id>`, not to a deeper page. The org's own entry page already decides where an org opens (and should check membership), so that decision stays in one place.

Leave the manifest `start_url`, the login callback and in-app links pointing at `/orgs`. Existing home-screen installs keep working without being re-added.

## Problems the Change Causes (fix with it)

- **A service worker that answers navigations hides the header.** A `fetch` handler that calls `event.respondWith(fetch(request))` for pages (as in [Hand-Rolled Service Worker](/architecture/pwa/hand-rolled-service-worker.md)) makes every request `same-origin`, so the redirect never fires. Let the browser fetch `/orgs` itself:

  ```javascript
  // The browser fetches /orgs itself so the middleware sees the real Sec-Fetch-Site (none on
  // a PWA launch) and can open the last org; a fetch from here would always be same-origin.
  if (url.pathname === "/orgs") return;
  ```

  The cost: offline, `/orgs` shows the browser's offline page. A worker with no `fetch` handler (push only) needs no change.
- **A deleted org must not become the launch target.** Where the app deletes an org (usually `settings/General.jsx`), clear the cookie before going back to `/orgs`:

  ```javascript
  document.cookie = `${LAST_ORG_COOKIE}=; path=/; max-age=0`;
  window.location.href = '/orgs';
  ```

## Known Limitations (note only)

- If you're removed from the org you visited last, the next launch opens that org's error page once. The "Orgs" breadcrumb leads back to the list, and the next org you open replaces the cookie.
- Playwright's WebKit sends `Sec-Fetch-Site: none` and redirects, but it isn't the iOS home-screen app. Check the home-screen launch on a real iPhone. If iOS doesn't redirect, the fallback is a separate entry URL (`start_url: /orgs?open=last`), which means re-adding the home-screen app.

## Tests

- **Middleware**: redirects when opened with the cookie set, and keeps the list for `same-origin`, for a missing `Sec-Fetch-Site` and for a missing cookie.
- **`RememberLastOrg`**: sets the cookie on a visit and follows a switch to another org.
- **On production** (Chromium, with the service worker active): open `/orgs` in a new tab and you land in the last org; click the "Orgs" breadcrumb and you get the list; refresh the list and it stays.

## Related Notes

- [Hand-Rolled Service Worker](/architecture/pwa/hand-rolled-service-worker.md) — its `fetch` handler is what needs the `/orgs` exception
- [Next.js Manifest and Icons](/architecture/pwa/nextjs-manifest-and-icons.md) — `start_url` stays `/orgs`
- [Solve Problems You Have Seen](/principles/solve-problems-you-have-seen.md) — why the limitations above are notes, not code
