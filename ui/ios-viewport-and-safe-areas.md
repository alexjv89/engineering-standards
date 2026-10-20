# iOS Viewport and Safe Areas

Keep Safari's default insets: **don't set `viewport-fit=cover`**. With the default, iOS keeps the page clear of the status bar and home indicator, and `env(safe-area-inset-*)` resolves to `0`.

## Pattern

```javascript
// src/app/layout.jsx
export const viewport = {
  width: 'device-width',
  initialScale: 1,
  themeColor: [
    { media: '(prefers-color-scheme: light)', color: '#ffffff' },
    { media: '(prefers-color-scheme: dark)', color: '#09090b' },
  ],
};
```

## Why Not `cover`

`viewport-fit=cover` draws the page edge to edge, *under* the status bar and Dynamic Island. Anything at `top: 0` is then hidden: in txn (2026-10-20) the sticky header, and with it the only button that opened navigation, disappeared under the status bar. Emulators don't draw the status bar, so it passed every automated check.

Use `cover` only when the design needs edge-to-edge (e.g. full-bleed media), and then pad every edge-touching element with `env(safe-area-inset-*)`, top included, and confirm on a real device.

## Inputs Must Be 16px on Phones

iOS zooms the page when an input under 16px gets focus. lib-ui's `Input` is `text-base md:text-sm`. Don't pass a `text-sm`/`text-xs` class to inputs that render on mobile.

## Fixed Elements

Floating buttons (`fixed bottom-6`) sit above Safari's floating toolbar only by luck. Leave content padding below them (e.g. `pb-20`) so they never cover the last row.

## Check

- [ ] No `viewportFit: 'cover'`, or every edge-touching element uses `env(safe-area-inset-*)` and a real device was checked
- [ ] Focused inputs at 390 wide compute to `font-size: 16px`
- [ ] Floating elements don't cover the last item when scrolled to the bottom
- [ ] Any change here is confirmed on a real iPhone (Safari and home-screen mode) before it's called done

## Related Notes

- [Mobile Document Scroll](/ui/mobile-document-scroll.md)
- [Visual Verification](/ui/visual-verification.md)
- [Next.js Manifest and Icons](/architecture/pwa/nextjs-manifest-and-icons.md)
