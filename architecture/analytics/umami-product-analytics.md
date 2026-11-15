# Product Analytics With Self-Hosted Umami

Every app sends product analytics to the self-hosted Umami at `umami.finopsbricks.com`, one Umami website per app, through `@fob/lib-ui/umami`. That covers pageviews, sessions, referrers and UTM attribution, clicks, form submits, file downloads, session replays and heatmaps, identified users and server-side events. Replays use strict masking, URLs carry no ids, and identify sends opaque ids only.

Reference implementation: `finopsbricks/apps/app-template` (`src/app/layout.jsx`, `src/components/navigation/AppSidebar.jsx`); the module is `finopsbricks/lib/lib-ui/src/umami/`.

## What Is Covered

| Feature | Where it comes from |
|---|---|
| Pageviews, sessions, referrers, UTM attribution, device and country | Umami tracker (`script.js`), automatic, including client-side route changes |
| Clicks on buttons and links, form submits, file downloads | `UmamiAnalytics` listens on the document and sends `button_click`, `link_click`, `form_submit`, `file_download` |
| Session replays and heatmaps | Umami recorder (`recorder.js`); the app's Umami website set to **strict** masking |
| Identified users | `UmamiIdentify` with the user id and org id |
| Server-side events (an upload, an export) | `trackServerEvent()` from `@fob/lib-ui/umami/server`, plus the app's `EventLogs` row |
| Funnels, journeys, retention, goals | Umami reports, built on the events above |

## The Pattern

### 1. Mount the tracker in the root layout

```javascript
// src/app/layout.jsx
import { UmamiAnalytics } from '@fob/lib-ui/umami';

<body>
  <UmamiAnalytics />
  ...
</body>
```

`UmamiAnalytics` reads `NEXT_PUBLIC_UMAMI_HOST_URL` and `NEXT_PUBLIC_UMAMI_WEBSITE_ID` and renders nothing when either is unset, so local development and a new app send nothing until their website exists. There is no fallback id in code. It doesn't track `/admin` or `/staff` (staff pages would pollute customer metrics). It loads, in order:

- the `data-before-send` handler (`beforeInteractive`, so it exists before the first pageview), which strips ids from every URL;
- the tracker, `script.js`;
- the recorder, `recorder.js`, for replays and heatmaps;
- the click, submit and download listeners.

### 2. Replays: strict masking, set on the Umami website

The recorder captures the DOM, and behind login that is balances, transactions and names. Umami sets masking per website: `moderate` masks inputs only, `strict` masks all text and inputs. **Every app's website is `strict`.** A replay still shows where people click, scroll, hesitate and give up, which is what replays are for; the numbers on screen are blurred.

Masking is a setting on the Umami server, not in code, so it is part of creating the website (Settings → Websites → the website → Replays & Heatmaps → Masking: Strict) and is checked on the first replay of a logged-in page. Only an auth-free site (`finopsbricks.com`) uses `moderate`.

### 3. URLs carry no ids

`umamiBeforeSend` rewrites the `url` and `referrer` of every payload:

- `/orgs/<anything>` → `/orgs/[id]`
- every UUID → `[id]`
- every numeric path segment → `/[id]`

Pages group by route instead of by record, and record ids stay out of the analytics store. Content slugs (`/convert/[bank]`) are left alone. An app with an id that is neither a UUID nor a number adds a rule for its path segment in lib-ui.

### 4. Events carry no page content

The listeners send what the user did, not what was on the screen:

- `link_click` and `file_download`: `{ href }`, the link's path with ids stripped. A link is a download when it has a `download` attribute or points at an export or a `.csv`, `.xlsx`, `.pdf` or `.zip`.
- `button_click`: `{ label }`, the button's `data-umami-label`, else its `aria-label`, else its text. A button whose text is data (an org switcher showing the org name, a row button showing a name) gets a `data-umami-label`.
- `form_submit`: `{ form }`, the form's `name` or `id`, never field values.

An element with Umami's own `data-umami-event` attribute is tracked by the tracker under that name and skipped by the listeners. Use it for an action worth naming in a report (`data-umami-event="statement_upload_start"`).

### 5. Identify with opaque ids only

```javascript
// src/components/navigation/AppSidebar.jsx
import { UmamiIdentify } from '@fob/lib-ui/umami';

<UmamiIdentify userId={user.id} orgId={params?.o_id} />
```

Render it once inside the signed-in layout. It waits for the tracker to load, then calls `umami.identify(user_id, { org_id })`, and again when the org changes. **Never send email or name**, and never use email as the id. To find one person's sessions, look up their user id.

### 6. Server events: `EventLogs`, then Umami

```javascript
import { trackServerEvent } from '@fob/lib-ui/umami/server';

await trackServerEvent('statement_upload', { user_id: user.id, org_id: org.id, url: '/api/v1/statements', data: { pages: 3 } });
```

The app's `track()` writes the event to its `EventLogs` table first (it powers the activity feed and is the record), then calls `trackServerEvent`. That posts to Umami's `/api/send` with the app's website id and the user id as the distinct id, so server events join the same user's browser session in reports. It never throws: a failed send is logged and dropped. Event data follows rule 4: ids and counts, no names, emails or amounts.

## Environment

```bash
# Product analytics (self-hosted Umami; unset = no tracking)
NEXT_PUBLIC_UMAMI_HOST_URL=https://umami.finopsbricks.com
NEXT_PUBLIC_UMAMI_WEBSITE_ID=
```

Both are `NEXT_PUBLIC_`, so they are read at build time: set them in the production env store before the deploy that should start tracking.

## Privacy Page

An app with a privacy page lists Umami as "product analytics and masked session replays, self-hosted on our own servers, no cookies" and lists no analytics vendor.

## Anti-Patterns

- An analytics or session-replay SDK other than Umami.
- A website with `moderate` masking for an app with a login.
- Email or name in identify, event data or the distinct id.
- Page text in event data (row labels, balances, names).
- A hard-coded fallback website id or API key, which sends local and preview traffic to production analytics.

## Check

- `grep -ri amplitude src package.json .env.example` finds nothing.
- On production, the network tab shows `script.js` and `recorder.js` from `umami.finopsbricks.com`, and `/api/send` requests whose `url` reads `/orgs/[id]/...`.
- A click on a button shows a `button_click` event in the app's Umami dashboard within a minute.
- The first replay of a logged-in page shows layout and clicks with every number and name blurred.

## Related Notes

- [Next.js Public Env Naming](/git-workflow/nextjs-public-env-naming.md) — why the tracker's settings are `NEXT_PUBLIC_`
- [Event Tracking & Analytics Pattern](/architecture/ai/event-tracking-analytics.md) — logging AI chat events to the database
