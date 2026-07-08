# Next.js App Router PWA Manifest and Icons

Next.js serves a web app manifest via the `src/app/manifest.js` file convention — no static `public/manifest.json`, no manual `<link rel="manifest">` tag.

## Pattern

```javascript
// src/app/manifest.js
export default function manifest() {
  return {
    name: "App Name",
    short_name: "App",
    start_url: "/",
    display: "standalone",
    background_color: "#ffffff",
    theme_color: "#111111",
    icons: [
      { src: "/icons/icon-192.png", sizes: "192x192", type: "image/png", purpose: "any" },
      { src: "/icons/icon-512.png", sizes: "512x512", type: "image/png", purpose: "any" },
      { src: "/icons/icon-maskable-512.png", sizes: "512x512", type: "image/png", purpose: "maskable" },
    ],
  };
}
```

Next.js auto-serves this at `/manifest.webmanifest` and injects the `<link>` tag.

## Icon Set Required

- **192×192 and 512×512** (`purpose: "any"`) — install icons on Android/desktop.
- **512×512 maskable** — a *separate* file with extra padding so the artwork survives being cropped to a circle/squircle by the OS. Keep visible content inside the inner ~80% (safe-zone radius ≈ 0.4 × size from center); a full-bleed background color with the mark centered comfortably clears this.
- **180×180 apple-touch-icon** — iOS ignores the manifest's `icons` array and reads this separately, via `metadata.icons.apple`, not the manifest file:

```javascript
// src/app/layout.js
export const metadata = {
  appleWebApp: { capable: true, statusBarStyle: "default", title: "App Name" },
  icons: { apple: "/icons/apple-touch-icon.png" },
};
```

## `themeColor` Moved Out of `metadata`

Next.js 14+ rejects `themeColor`/`colorScheme` inside the `metadata` export — they belong in a separate `viewport` export:

```javascript
export const viewport = {
  themeColor: "#111111",
};
```

## Related Notes
- [Turbopack/Service Worker Incompatibility](/architecture/pwa/turbopack-service-worker-incompatibility.md)
- [Install Prompt UX](/architecture/pwa/install-prompt-ux.md)
