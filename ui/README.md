# UI Standards

How FinOpsBricks app UIs must behave and how UI changes are made and checked. Visual tokens and components come from `@fob/lib-ui`. These notes cover layout behaviour, mobile and the discipline around UI changes. Each note ends with a **Check** list that a reviewer or role runs against the change.

## Which Notes to Load

| You are… | Read |
|---|---|
| Making any UI change | [Change Scope](/ui/change-scope.md), [Visual Verification](/ui/visual-verification.md), [Regression Stories](/ui/regression-stories.md) |
| Touching page layout, scrolling or the app shell | [Mobile Document Scroll](/ui/mobile-document-scroll.md) |
| Touching `viewport`, safe areas, PWA or iOS behaviour | [iOS Viewport and Safe Areas](/ui/ios-viewport-and-safe-areas.md), `/architecture/pwa/` |
| Building or changing a page header | [Page Header on Mobile](/ui/page-header-mobile.md) |
| Showing a list or table | [Tables on Mobile](/ui/tables-on-mobile.md) |

Shipping the change afterwards: [Ship and Verify](/deployment/ship-and-verify.md).

## Breakpoints

Use Tailwind's `md` (768px) as the phone/desktop line. It matches `useIsMobile()` and the lib-ui sidebar. Verify at **390** (phone), **834** (tablet) and **1440** (desktop).

## Related Notes

- [Solve Problems You Have Seen](/principles/solve-problems-you-have-seen.md)
- [Horizontal Scroll Container](/architecture/components/horizontal-scroll-container.md)
- [PWA patterns](/architecture/pwa/nextjs-manifest-and-icons.md)
