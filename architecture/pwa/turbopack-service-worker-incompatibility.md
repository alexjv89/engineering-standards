# Turbopack Breaks Framework-Based Service Worker Plugins (Next.js 16)

Next.js 16 defaults to Turbopack for both `dev` and `build`. Service worker frameworks built for Next.js — Serwist (`@serwist/next`) and its predecessor `next-pwa` — inject a **webpack** config via `withSerwistInit(nextConfig)` / `withPWA(nextConfig)`. That conflicts with Turbopack and the build fails:

```
⨯ ERROR: This build is using Turbopack, with a `webpack` config and no `turbopack` config.
```

## Why

These plugins pre-date Turbopack-as-default and generate the precache asset manifest via a webpack loader. There's no mature Turbopack equivalent yet:

- `@serwist/turbopack` exists but is explicitly experimental / preview-only (`10.0.0-preview.x` as of Next.js 16) — not something to put in a production build pipeline.
- Forcing the whole project onto webpack (`next build --webpack`) works but reverses a project-wide bundler decision Next.js just made the default — too large a change just to get a service worker.

## Resolution Used

Dropped the framework, hand-wrote the service worker as a **static file** in `public/sw.js` (see [Hand-Rolled Service Worker](/architecture/pwa/hand-rolled-service-worker.md)). A static file has no build step, so it's bundler-agnostic — it sidesteps the Turbopack/webpack conflict entirely.

## When to Reconsider

Once `@serwist/turbopack` (or an equivalent) reaches stable, re-evaluate — precaching driven by the actual build manifest (vs. a hand-maintained URL list) is more correct for apps with many static assets.

## Related Notes
- [Hand-Rolled Service Worker](/architecture/pwa/hand-rolled-service-worker.md)
- [Next.js App Router Manifest and Icons](/architecture/pwa/nextjs-manifest-and-icons.md)
