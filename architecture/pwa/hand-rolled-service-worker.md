# Hand-Rolled Service Worker (No Build Step)

When a framework-based service worker isn't viable (see [Turbopack incompatibility](/architecture/pwa/turbopack-service-worker-incompatibility.md)), a small hand-written `public/sw.js` covers installability and basic offline fallback with zero bundler integration.

## Pattern

```javascript
// public/sw.js
const CACHE_NAME = "app-v1";
const PRECACHE_URLS = ["/", "/manifest.webmanifest", "/icons/icon-192.png"];

self.addEventListener("install", (event) => {
  event.waitUntil(caches.open(CACHE_NAME).then((cache) => cache.addAll(PRECACHE_URLS)));
  self.skipWaiting();
});

self.addEventListener("activate", (event) => {
  event.waitUntil(
    caches.keys().then((keys) =>
      Promise.all(keys.filter((k) => k !== CACHE_NAME).map((k) => caches.delete(k))),
    ),
  );
  self.clients.claim();
});

self.addEventListener("fetch", (event) => {
  const { request } = event;
  const url = new URL(request.url);
  if (request.method !== "GET" || url.origin !== self.location.origin || url.pathname.startsWith("/api/")) {
    return; // let API/mutation requests pass through untouched
  }
  event.respondWith(
    fetch(request)
      .then((response) => {
        const copy = response.clone();
        caches.open(CACHE_NAME).then((cache) => cache.put(request, copy));
        return response;
      })
      .catch(() => caches.match(request).then((cached) => cached || caches.match("/"))),
  );
});
```

## Key Decisions

- **Exclude `/api/*` from caching** — network-first-with-cache-fallback on API routes risks serving stale or session-specific JSON from a shared browser cache. Restrict runtime caching to pages/static assets.
- **Bump `CACHE_NAME` to invalidate** — there's no build tooling computing a content hash, so cache-busting is a manual version string.
- **Register production-only** — a caching SW fights hot reload in dev:

```javascript
// client component, mounted once in the root layout
useEffect(() => {
  if (process.env.NODE_ENV === "production" && "serviceWorker" in navigator) {
    navigator.serviceWorker.register("/sw.js").catch(() => {});
  }
}, []);
```

## Related Notes
- [Turbopack/Service Worker Incompatibility](/architecture/pwa/turbopack-service-worker-incompatibility.md)
- [Web Push Overview](/architecture/pwa/web-push-overview.md)
