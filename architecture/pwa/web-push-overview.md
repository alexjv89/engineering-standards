# Web Push Notifications: How the Pieces Fit

Reference for when push gets implemented — not built yet on this stack. Push requires a service worker; it does not work from a plain browser tab with none registered.

## Flow

1. **Permission** — `Notification.requestPermission()`, triggered by a user gesture (never on page load; a denial is often permanent until the user resets it manually in browser settings).
2. **Subscribe** — the client asks the service worker's `pushManager` for a subscription, authenticated with a VAPID key pair:
   ```javascript
   const subscription = await registration.pushManager.subscribe({
     userVisibleOnly: true,
     applicationServerKey: VAPID_PUBLIC_KEY,
   });
   ```
   `subscription` is `{ endpoint, keys }` — send it to the backend and store one per browser/device (no cross-device sync exists).
3. **Send** — the server VAPID-signs a request to `subscription.endpoint` (commonly via the `web-push` npm package). The endpoint belongs to the browser vendor's push service (FCM for Chrome, Mozilla autopush for Firefox, Apple's push service for Safari) — the server never talks to the device directly.
4. **Deliver** — the browser wakes the service worker even with no tab open; its `push` handler calls `self.registration.showNotification(...)`, and a `notificationclick` handler routes the tap (e.g. open a specific page).

## iOS Constraint

Web Push on iOS Safari (16.4+) only works for an **installed** PWA (Add to Home Screen) — a regular Safari tab gets nothing, ever. This ties push directly to solving [install-prompt UX](/architecture/pwa/install-prompt-ux.md) first on iOS; there's no way to ship push to iOS users who haven't installed.

## Related Notes
- [Hand-Rolled Service Worker](/architecture/pwa/hand-rolled-service-worker.md)
- [Install Prompt UX](/architecture/pwa/install-prompt-ux.md)
