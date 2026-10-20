# Page Header on Mobile

On phones the page title and its actions share **one row**: title on the left, actions on the right. The header doesn't stack into a column that puts one small button on a line of its own.

## Why

Stacking a short title above a short button leaves most of both lines empty, and the list the user came for gets pushed down. On a 390 × 664 screen, one wasted row is ~6% of the viewport, on every page. (txn transactions, 2026-10-20: "Transactions" then "Export CSV" on its own line, with the stats below.)

## Pattern

```jsx
<div className="flex items-center gap-2">
  <h1 className="min-w-0 flex-1 truncate">{title}</h1>
  <div className="flex shrink-0 items-center gap-2">{actions}</div>
</div>
```

When actions don't fit next to the title at 390:

1. **Icon buttons** below `sm`: keep the icon, hide the label visually, and give the button an `aria-label`.
2. **Overflow menu**: more than two actions go into a `⋯` dropdown on phones.
3. Never shrink the title text to make room. Truncate it instead.

Wrapping only kicks in when the title itself is too long to fit next to the actions.

### What lib-ui `PageHeader` does (v0.6.3)

- **1–2 actions:** one row; the title ellipsizes when space runs out.
- **Actions wider than the room left for a ~128px title** (3+ labelled buttons): the actions wrap onto their own row below the title. `PageHeader` can't turn a consumer's buttons into icons or a menu, so for those pages that's the consumer's job (options 1–2 above).
- **No actions:** the title wraps normally. Never ellipsize a title that has the whole row to itself.

## Where to Fix

The header layout belongs in the shared lib-ui `PageHeader` (fixed in v0.6.3), so every app gets it by bumping lib-ui. Don't override it per page.

## Check

- [ ] At 390 wide, the title and actions are on the same row for every page that has ≤ 2 actions
- [ ] Icon-only buttons have an `aria-label` and a ≥ 36px tap target
- [ ] A long title truncates; it doesn't push the actions off-screen
- [ ] A title with no actions wraps instead of truncating
- [ ] Pages with 3+ labelled actions use icon buttons or an overflow menu on phones, or accept the wrapped row deliberately
- [ ] At 1440 the header is unchanged

## Related Notes

- [Change Scope](/ui/change-scope.md)
- [Visual Verification](/ui/visual-verification.md)
- [Tables on Mobile](/ui/tables-on-mobile.md)
