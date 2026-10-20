# Regression Stories

Every UI component you add or change gets Storybook stories at phone and desktop widths, so the `storybook:test` job in CI renders it on every push. Screenshots verify a change once; stories keep checking it.

## Pattern

One story per meaningful state, with the viewport set per story:

```javascript
// TransactionCards.stories.jsx
import TransactionCards from './TransactionCards';
import { transactions } from './fixtures';

export default { title: 'Transactions/TransactionCards', component: TransactionCards };

export const Mobile = {
  args: { transactions, o_id: 'acme' },
  globals: { viewport: { value: 'mobile1', isRotated: false } },
};

export const MobileEmptyCounterparty = {
  args: { transactions: [{ ...transactions[0], third_party: null }], o_id: 'acme' },
  globals: { viewport: { value: 'mobile1', isRotated: false } },
};
```

- Components that switch layout at `md` need a story **on each side** of the breakpoint.
- Use realistic fixtures: long counterparties, multiple currencies, empty fields.
- Add a `play` function when behaviour matters (edit a comment, press "Show more").

## What CI Gives You

`storybook:test:ci` fails when a story throws, a `play` assertion fails, or (with `addon-a11y`) an accessibility check fails. It does not diff pixels. Pair stories with [Visual Verification](/ui/visual-verification.md) for the look.

## Check

- [ ] Each added or changed component has stories, at mobile width and, if it changes at `md`, at desktop width
- [ ] Fixtures include long text, missing fields and every variant the component branches on
- [ ] `npm run storybook:test:ci` passes locally before pushing

## Related Notes

- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md)
- [Play Functions](/storybook/play-functions/basics.md)
- [Storybook Setup: viewports](/storybook/setup-storybook.md)
- [Visual Verification](/ui/visual-verification.md)
