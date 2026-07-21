# Generate Higher Scopes From Lower

Higher-scope status should be **derived** from lower-scope files, not hand-maintained in parallel. An org status that is a rollup of every account status cannot disagree with them — it is recomputed from them on every run.

## The pattern

- **Source of truth = the finest scope** that owns each fact (a period worksheet, an account's to-do).
- **Coarser status = generated** by rolling up finer files. Regenerated each run → drift-proof by construction.
- **Hand-maintained surface = as small as possible** — only the assertions no computation can derive: what a human still must do (to-do) and what happened (log).

## Example (reconciliation)

```
period-status   ← generated from source data
account-status  ← generated: rolls up this account's periods
org-status      ← generated: rolls up all account-status
account-to-do   ← hand-written (a found fix)
org-to-do       ← generated index of account-to-dos + hand-written org-scoped items
logs            ← hand-appended
```

Three file kinds are ever hand-touched; everything else falls out.

## Why

A hand-maintained summary is a second copy of the detail (see [Scope Hierarchy](/principles/working-docs/scope-hierarchy.md)) — it drifts the moment someone updates a child and forgets the parent. Generating it removes the copy entirely, so there is nothing to keep in sync.

## Related Notes
- [Scope Hierarchy](/principles/working-docs/scope-hierarchy.md)
- [Working-Doc Report Types](/principles/working-docs/report-types.md)
- [Keep Derivations Out of Operations](/principles/working-docs/derivations-stay-out-of-operations.md)
