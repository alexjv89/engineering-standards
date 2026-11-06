# UI Standards

How UI changes are made and checked. **What the UI looks like and how it behaves** (layout, responsive rules, colour, components, patterns) lives in the design system, [design-standards](https://design.echoalex.com) (repo `~/ec2code/alex/design-standards`). Tokens and components come from `@fob/lib-ui`. These notes cover the discipline around a UI change. Each ends with a **Check** list that a reviewer or role runs against the change.

## Which Notes to Load

| You are… | Read |
|---|---|
| Making any UI change | [Change Scope](/ui/change-scope.md), [Visual Verification](/ui/visual-verification.md), [Regression Stories](/ui/regression-stories.md) |
| Touching page layout, scrolling, the app shell, viewport or safe areas, page headers, or tables on phones | design-standards [Responsive](https://design.echoalex.com/foundations/responsive) |
| Picking a colour, or making a component work in dark mode | design-standards [Colour is a distance from the surface](https://design.echoalex.com/primitives/colour-is-a-distance-from-the-surface) |
| Changing a landing page (design or content) | [Landing Page Versions](/ui/landing-page-versions.md) |

Shipping the change afterwards: [Ship and Verify](/deployment/ship-and-verify.md).

## Breakpoints

The phone/desktop line and the widths to check are a design rule: [md is the phone/desktop line](https://design.echoalex.com/foundations/responsive/md-is-the-phone-desktop-line).

## Related Notes

- [Solve Problems You Have Seen](/principles/solve-problems-you-have-seen.md)
- [Horizontal Scroll Container](/architecture/components/horizontal-scroll-container.md)
- [PWA patterns](/architecture/pwa/nextjs-manifest-and-icons.md)
