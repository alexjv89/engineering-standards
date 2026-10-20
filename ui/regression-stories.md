# Regression Stories

Every UI component you add or change gets Storybook stories at phone and desktop widths, so the `storybook:test` job in CI renders it on every push. Screenshots verify a change once; stories keep checking it.

## Pattern

One story per meaningful state, with the viewport set per story:

```javascript
// TransactionCards.stories.jsx
import TransactionCards from './TransactionCards';
import { within, expect } from 'storybook/test';
import { transactions } from './fixtures';

export default { title: 'Transactions/TransactionCards', component: TransactionCards };

export const Phone = {
  args: { transactions, o_id: 'acme' },
  globals: { viewport: { value: 'iphone13', isRotated: false } }, // 390×844
  play: async ({ canvasElement }) => {
    await expect(within(canvasElement).queryByRole('table')).toBeNull();
  },
};

export const PhoneEmptyCounterparty = {
  args: { transactions: [{ ...transactions[0], third_party: null }], o_id: 'acme' },
  globals: { viewport: { value: 'iphone13', isRotated: false } },
};
```

- `mobile1` is 320px wide, not a 390 phone; use `iphone13`.
- **The test runner must honour the story's viewport.** Apps whose `.storybook/test-runner.js` sets one fixed size in `preVisit` render every story at desktop width in CI, so a phone story passes without ever showing the phone layout. Size the page from `storyGlobals.viewport` instead (the `regression-stories` skill in `roles/ux-designer` has the snippet).
- A **play function asserts the layout for that width** (cards vs table, no sideways scroll). Without an assertion, a story rendered at the wrong width still passes.

- Components that switch layout at `md` need a story **on each side** of the breakpoint.
- Use realistic fixtures: long counterparties, multiple currencies, empty fields.
- Add `play` steps for behaviour (edit a comment, press "Show more").

## What CI Gives You

`storybook:test:ci` fails when a story throws, a `play` assertion fails, or (with `addon-a11y`) an accessibility check fails. It does not diff pixels. Pair stories with [Visual Verification](/ui/visual-verification.md) for the look.

## Check

- [ ] Each added or changed component has stories, at mobile width and, if it changes at `md`, at desktop width
- [ ] `test-runner.js` sizes the page from the story's viewport, and each phone story's play function would fail at desktop width (break it once to confirm)
- [ ] Fixtures include long text, missing fields and every variant the component branches on
- [ ] `npm run storybook:test:ci` passes locally before pushing

## Related Notes

- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md)
- [Play Functions](/storybook/play-functions/basics.md)
- [Storybook Setup: viewports](/storybook/setup-storybook.md)
- [Visual Verification](/ui/visual-verification.md)
