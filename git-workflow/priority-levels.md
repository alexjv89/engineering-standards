# Priority Levels (P0–P4)

Every piece of planned work carries a priority from **P0** (drop everything) to **P4**
(backlog). The level is set from two things only: **how urgent** it is and **how much it
affects** the people using the product. It says when the work gets done, not how big or how
far along it is.

## The Levels

| Level | Urgency | Impact | When it's worked on | Typical example |
|---|---|---|---|---|
| **P0** | Critical | Everyone, or the core job can't be done | **Now, at any hour.** Everything else stops until it's fixed | Production is down; sign-in fails for every user; data is being lost or exposed; money is moving wrongly |
| **P1** | High | Many users, or a main feature is broken (with no good workaround) | **Today or the next working day**, in working hours. Only P0 comes before it | File upload fails on a common phone; approvals can't be submitted; the deploy pipeline is broken |
| **P2** | Moderate | Some users, or a secondary feature; a workaround exists | **Planned into the current cycle** | A filter returns wrong rows; an important improvement users are waiting for |
| **P3** | Low | A few users, or polish | **When convenient**, never ahead of P0–P2 | Cosmetic issues; small UX refinements; "it would be great if" |
| **P4** | Negligible now | No current user is affected | **Not scheduled.** Backlog until there's spare capacity or something raises it | Preparing for a future architecture; experiments; wishlist ideas |

## Choosing a Level

Ask in order and stop at the first yes:

1. **Is it on fire?** The product is down, data is being lost or exposed, or money is going
   wrong, so someone must act now, even outside working hours. → **P0**
2. **Is a main job broken for many users, with no good workaround?** It needs attention this
   working day, but not at night. → **P1**
3. **Does it hurt some users or a secondary feature, or is it an improvement that matters this
   cycle?** → **P2**
4. **Is it a refinement or a nice-to-have that someone would notice?** → **P3**
5. **Otherwise** (no one is affected today) → **P4**

**P0 vs P1 is a question of time, not size.** P0 means "wake someone up". P1 means "first
thing in working hours". If waiting until morning is acceptable, it's P1.

**P1 vs P2 is a question of reach and workaround.** Many users with no way around it is P1;
fewer users, or a workaround that people can live with, is P2.

Bugs and features use the same scale. In practice P0 and P1 are almost always defects or
incidents, and new features usually start at P2 or below.

## Rules

- **Write the reason next to the level**, in one line: `P1: uploads fail for iPhone users`.
  A level without a reason can't be checked or argued with.
- **Be stingy with P0 and P1.** If many items are P0 or P1, nothing is. Only P0 interrupts
  other work.
- **Priority is not status or size.** A P4 item can be half done; a P0 can be a one-line fix.
  Keep the three separate.
- **Re-prioritise when the facts change**, not on a calendar. A P4 becomes P2 when users start
  hitting it; a P1 becomes P3 when a workaround appears. Change the level and its reason
  together.
- **Don't pull a lower level ahead of a higher one** because it's easier or more interesting.

## Where It's Written

- WIP files: a `## Priority:` line under the status (see [WIP Files](/git-workflow/wip-files.md)).
- Issues: the tracker's priority field, using the same five levels.

## Related Notes

- [WIP Files](/git-workflow/wip-files.md) - every WIP carries a priority
- [Solve Problems You Have Seen](/principles/solve-problems-you-have-seen.md) - imagined
  problems are written down, which usually means P4
- [Decision Records](/git-workflow/decision-records.md) - one-way vs two-way doors, which
  sizes how much care a decision needs (a separate question from when it's done)
