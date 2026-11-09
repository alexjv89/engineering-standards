# Landing Page Versions

A landing page changes through frozen versions served next to the live page, at `/landing/<version>`. Each version changes one thing, so whoever judges it can tell what that change did. `/` is a separate copy that is edited on its own.

## The Rule

```
src/landing/README.md           version table + how to add a version
src/landing/v1/Page.jsx         frozen copy of the page that was live when versioning started
src/landing/v2/                 a version: Page.jsx + copies of every visual part it changes
src/app/(site)/page.jsx         the live `/`: its own code, never imports a version
src/app/(site)/landing/v1/      thin route for a version that uses the live site chrome
src/app/landing/v2/             thin route (+ layout) for a version with its own chrome
```

(`(site)` is `(marketing)` in an app whose public pages already sit in that group.)

- **Frozen.** Once committed, a version folder never changes. The next change is a new folder copied from its parent: `cp -R src/landing/v1 src/landing/v1.1`.
- **Point or major.** A point version (`v1.1`) changes one dimension, and the page still reads as the same page. A major version (`v2`) changes how the whole page reads: the hero visual, a theme flip, or a new section order.
- **`/` is a copy, not a pointer.** Making a version live means copying its page into the live route and noting the sync date in the README. `/` can then be edited without touching any version, and no version can change by accident.
- **Copy what you change.** A version copies a shared component (header, footer, wordmark, theme) into its folder before changing it. It never edits the shared one, because that would change the live site and every other version too.
- **Theme scoped to the version.** App-router CSS stays loaded after client navigation, so a version's tokens go under `[data-landing="<version>"]` on its wrapper, never `:root`. With Tailwind v4, the version's theme file is `@import`ed from `src/app/globals.css`, because `@theme` colours register only from the main entry and a second Tailwind build would re-emit utilities over the live site's. This import is the one line a version adds to a shared file.
- **Out of search.** Version routes set `robots: { index: false, follow: true }` and a canonical pointing to `/` (absolute when the app has no `metadataBase`), and stay out of the sitemap. Don't block them in `robots.txt`: crawlers would then never see the noindex. They're built and compared on localhost first (a set of versions side by side, each choice with its reason); the set ships to production once it's been judged, so it can be checked on a real phone and shared.
- **README table:** `Version | Date | What it is | Parent | What changed vs parent | Commit | Reaction`. Record the reaction word for word before the next version builds on this one.

## Why

- In 2026-10, three landing pages were redesigned in one diff each: theme, structure, proof and copy together. The reviewer loved the look but was confused by the content, and couldn't tell which change caused it. One change per version makes each reaction mean something.
- Uncommitted redesigns sat in three working trees for days with nowhere to go except live or lost. Versions let work ship without replacing the live page.
- Wiring `/` to a version means the next edit to `/` silently rewrites history. A copy keeps both safe.

## Moving the Chrome Into a Route Group

The first time an app gets a version with its own chrome, the root layout's header and footer move into a `(site)` route group, with a mechanical `git mv` of the existing routes, in its own commit. Next renders 404s in the root layout only, so add `src/app/not-found.jsx` wrapped in the site chrome. Otherwise the 404 page loses its header and footer.

## Check

- [ ] The new version is a new folder; no existing version's folder and no `/` code changed
- [ ] The version's diff against its parent shows one dimension only
- [ ] Theme tokens are scoped to `[data-landing="<version>"]`; navigating from the version to `/` client-side leaves `/` unchanged
- [ ] The route renders `noindex, follow` and the canonical; the sitemap has no `/landing` URLs
- [ ] `/` and the other routes are pixel-identical to before ([Visual Verification](/ui/visual-verification.md))
- [ ] README row added

## Related Notes

- [Change Scope](/ui/change-scope.md): one dimension per version is change scope applied to landing pages
- [Visual Verification](/ui/visual-verification.md)
- [Try It to Decide](/principles/try-it-to-decide.md): a version is the smallest thing that answers a taste question
- Role that works this way: `~/ec2code/alex/roles/roles/landing-page-designer`
