# Change Scope

A UI change touches what the request asked for, plus whatever the change itself breaks. Working components are not rewritten along the way, and extra improvements are proposed, not shipped.

## The Rule

Sort everything you notice into three piles:

| Pile | Example | Action |
|---|---|---|
| **Asked** | "Transactions table is unusable on mobile" | Do it |
| **Caused** | Your layout change un-sticks the header | Fix it in the same change |
| **Noticed** | Stats bar scrolls sideways; PWA manifest missing | Write it in the WIP as a proposal; ask before doing it |

"It would be better" is not enough to put a noticed item in the asked pile. The user knows what they asked for and will see every diff line as a claim that they wanted it.

## Why

- Unasked rewrites cost review time and replace a UI the user was happy with.
- Each extra file is extra risk. In the txn mobile session (2026-10-20), unasked PWA files in `public/` broke the production deploy, and an unasked StatsBar rewrite was rejected.
- A refactor bundled with a fix makes the fix hard to review or revert.

## Small Exceptions

Moving code is fine when the asked change needs it (e.g. extracting a cell so table and cards can share it). Keep the move mechanical and say so in the commit.

## Check

- [ ] Every changed file traces to the request or to something the change itself broke
- [ ] No component that worked before is restyled or restructured beyond what the request needs
- [ ] Noticed-but-unasked items are listed as proposals in the WIP or summary, not implemented
- [ ] Desktop is untouched when the request was about mobile (verify, don't assume)

## Related Notes

- [Solve Problems You Have Seen](/principles/solve-problems-you-have-seen.md)
- [Visual Verification](/ui/visual-verification.md)
- [Abstraction Must Earn Its Keep](/principles/abstraction-earns-its-keep.md)
