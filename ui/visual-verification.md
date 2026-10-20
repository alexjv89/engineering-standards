# Visual Verification

A UI change is verified by comparing screenshots taken **before** and **after** the change, at every breakpoint, and reading them. Checking that the new code renders is not verification.

## Procedure

1. **Before.** Screenshot every affected page or story at 390, 834 and 1440 wide, in each state that matters (default, empty, loading, long content).
2. **Change.**
3. **After.** Same pages, same widths, same states.
4. **Compare pairs.** For each pair, ask:
   - Did what was asked change?
   - Did anything else change? Unasked differences are regressions until explained.
   - Is there anything off in the *after* shot, even if it was already there (wasted space, clipping, overlap)? On a page the request is about, fix it or raise it; don't call it fine.

Use Storybook stories or the real app with real-shaped data. Don't build a throwaway route with invented data, because it hides how real content wraps and truncates.

## What Emulation Can't Show

Playwright's iPhone emulation has the right viewport and engine, but no iOS browser chrome. It does **not** show:

- the status bar / Dynamic Island overlapping the page
- Safari's floating toolbar and whether it collapses
- home-indicator overlap, standalone (home-screen) PWA mode

Changes that depend on these ([iOS Viewport and Safe Areas](/ui/ios-viewport-and-safe-areas.md)) need a **real-device check by a person** before shipping. Ask for it explicitly; never report them as verified from emulation.

## Check

- [ ] Before and after screenshots exist for 390 / 834 / 1440 and were read back
- [ ] Every difference between the pairs is either asked for or explained
- [ ] Problems visible in the after shots of the requested pages are fixed or raised
- [ ] iOS-chrome-dependent changes have a real-device confirmation, or are reported as unverified

## Related Notes

- [Regression Stories](/ui/regression-stories.md)
- [Change Scope](/ui/change-scope.md)
- [Try It to Decide](/principles/try-it-to-decide.md)
