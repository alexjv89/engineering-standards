# Tables on Mobile

A table whose key value goes off-screen at 390 wide becomes a **card list** below `md`. Horizontal scrolling through a wide table is not a mobile layout.

## When to Switch

Render the table at 390 wide. If the column the user reads the row for (amount, status, total) isn't visible without scrolling sideways, use cards. A 2–3 column table that fits can stay a table.

## What a Card Shows

| Line | Content |
|---|---|
| 1 | Primary label (counterparty, name), clamped to 2 lines, **and** the key value right-aligned, never truncated |
| 2 | Secondary facts in muted small text, joined with ` · ` (time, account) |
| 3 (optional) | Inline actions: tap targets ≥ 36px tall |

- The card body links to the row's detail page; inline actions sit outside that link (no interactive elements nested in a link).
- Long lists group under sticky headings when the table was sorted by a grouping column (e.g. date).
- Page with "Show N more" instead of Previous/Next.
- Same order and filters as the table: the cards are a view, not a different query.

## Switching

Switch on `useIsMobile()` from lib-ui, in the component that owns the table, so only one of the two is mounted. Keep the table untouched on desktop.

## Check

- [ ] At 390 wide, no horizontal page scroll (`document.body.scrollWidth === innerWidth`)
- [ ] The key value is visible on every card, including with the longest real label
- [ ] Tap targets in cards are ≥ 36px tall; inputs are 16px
- [ ] At 1440 the table is identical to before
- [ ] Stories cover both sides of the breakpoint ([Regression Stories](/ui/regression-stories.md))

## Related Notes

- [Change Scope](/ui/change-scope.md)
- [Container/Presentational Pattern](/architecture/components/container-presentational-pattern.md)
- [Horizontal Scroll Container](/architecture/components/horizontal-scroll-container.md)
