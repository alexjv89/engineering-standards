# PWA Install Prompt: Platform-Specific UX

There is no single "install this app" API. Android/desktop Chromium browsers expose a programmatic prompt; iOS Safari does not.

## Android / Desktop Chrome, Edge

Listen for `beforeinstallprompt`, stash the event, and trigger it from your own UI — the browser's automatic mini-infobar only fires once, so you need the saved event to show a prompt on demand later:

```javascript
window.addEventListener("beforeinstallprompt", (event) => {
  event.preventDefault(); // stop the automatic mini-infobar
  deferredPrompt = event; // save it for a later user click
});

// later, on a user click:
deferredPrompt.prompt();
await deferredPrompt.userChoice;
```

## iOS Safari

No `beforeinstallprompt`, no programmatic API at all. The only path is manual: "Tap Share → Add to Home Screen." Detect iOS Safari and show static instructions instead of a button:

```javascript
function isIosSafari() {
  const ua = window.navigator.userAgent;
  const isIos = /iPad|iPhone|iPod/.test(ua);
  const isSafari = /^((?!chrome|android|crios|fxios).)*safari/i.test(ua);
  return isIos && isSafari;
}
```

## Don't Nag Installed Users

Check standalone display mode before showing any install UI:

```javascript
function isStandalone() {
  return (
    window.matchMedia("(display-mode: standalone)").matches ||
    window.navigator.standalone === true // iOS-specific flag
  );
}
```

These checks are browser-only — see [Reading Browser-Only State on Mount](/architecture/state/browser-only-state-on-mount.md) for how to use them in a component without a hydration mismatch.

## Related Notes
- [Reading Browser-Only State on Mount Without Hydration Mismatches](/architecture/state/browser-only-state-on-mount.md)
- [Web Push Overview](/architecture/pwa/web-push-overview.md)
