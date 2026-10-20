# Mobile Document Scroll

On phones the **document** scrolls. Below `md`, don't use a fixed-height app shell (`height: 100vh; overflow: hidden` on `html`/`body`) that scrolls each page inside a nested div. Desktop may keep the app shell.

## Why

On iOS Safari:

- `100vh` is the **large** viewport, the height with the toolbar hidden. While the toolbar shows, a `100vh` shell is taller than the screen, and its bottom strip sits under the toolbar. The last item of every page can't be scrolled into view.
- Safari only collapses its toolbar, and tap-the-status-bar only scrolls to top, when the **document** scrolls. Inside a nested scroller neither happens.

## Pattern

```css
/* globals.css — lock the shell on desktop only */
@media (min-width: 768px) {
  html, body { overflow: hidden; height: 100%; }
}
```

```jsx
{/* root layout wrapper */}
<div className="min-h-dvh flex flex-col md:h-dvh md:overflow-hidden">{children}</div>
```

Below `md` the inner `overflow-y-auto flex-1 min-h-0` containers have no fixed height, so they grow and the page scrolls normally. Use `dvh`, never `vh`/`h-screen`, for any full-height box.

## Sticky Headers Need a Clean Ancestor Chain

`position: sticky` sticks to the nearest ancestor with `overflow` other than `visible`. An `overflow: hidden` wrapper (lib-ui's sidebar wrapper and inset both have one) stops the header sticking during document scroll. On mobile, switch those to `overflow: clip`, which clips without becoming a scroll container:

```css
@media (max-width: 767px) {
  [data-slot="sidebar-wrapper"], [data-slot="sidebar-inset"] { overflow: clip; }
}
```

## Check

- [ ] At 390 wide, `document.scrollingElement.scrollHeight > innerHeight` on a long page, and `body` overflow is `visible`
- [ ] Scrolled to the bottom, the last item's `getBoundingClientRect().bottom <= innerHeight`
- [ ] Scrolled down, the sticky header's `top` is `0`
- [ ] At 1440 the shell is unchanged: document does not scroll, panes scroll independently
- [ ] No `100vh` / `h-screen` left in layout code

## Related Notes

- [iOS Viewport and Safe Areas](/ui/ios-viewport-and-safe-areas.md)
- [Visual Verification](/ui/visual-verification.md)
- [Horizontal Scroll Container](/architecture/components/horizontal-scroll-container.md)
