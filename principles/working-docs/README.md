# Working Docs

Working docs are the **mutable operational records** a process keeps about its own runs — the live answer to "where are we, what's left, what happened." They are distinct from **documentation** (timeless knowledge): a working doc changes every time you *do* the work; a knowledge note changes only when you *learn* something new. See [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md) for that split — working docs are the "report" side, and these notes standardize their shape.

They come in two shapes: **standing** docs that track an operation which never ends (the scope × type grid below), and **[jobs](/principles/working-docs/jobs.md)** — bounded, checklist-first efforts that open, get worked, and close (the operational counterpart to an engineer's [WIP file](/git-workflow/wip-files.md)).

## Two axes: scope × type

Every working doc is exactly one **type** at one **scope**.

- **Scopes** nest, coarse → fine. Example (bookkeeping): org → account → period.
- **Types**: [status, to-do, log](/principles/working-docs/report-types.md).

Not every scope needs every type. A worked grid (from a reconciliation effort):

| Scope | status | to-do | log |
|---|---|---|---|
| **org** | roster of accounts | app changes needed | org-scoped events |
| **account** | months + balances | this account's fixes | this account's events |
| **period** | the worksheet | — | — |

## The rules

- [Report types](/principles/working-docs/report-types.md) — what status / to-do / log each mean, and their change driver.
- [Scope hierarchy](/principles/working-docs/scope-hierarchy.md) — place each item at the lowest scope that contains it; higher scopes link, never repeat (repetition = drift).
- [Generate higher scopes from lower](/principles/working-docs/generated-rollups.md) — coarser status derives from finer files, so it can't drift; keep the hand-maintained surface tiny.
- [Instances carry no system-docs](/principles/working-docs/instances-carry-no-system-docs.md) — a file never explains the working-doc system; that lives here.
- [Status has no tombstones](/principles/working-docs/status-has-no-tombstones.md) — status shows only what currently exists.
- [Log entry format](/principles/working-docs/log-entry-format.md) — nested bullets, newest first.
- [Keep derivations out of operations](/principles/working-docs/derivations-stay-out-of-operations.md) — case studies etc. are generated on demand, not maintained.
- [Jobs](/principles/working-docs/jobs.md) — a bounded, checklist-first doc for one discrete effort; opens, gets worked, closes (vs. the standing types above).

## Related Notes
- [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md)
- [One Change Driver Per Artifact](/principles/documentation/one-change-driver.md)
- [Hub and Spoke Doc Placement](/principles/documentation/hub-spoke-doc-placement.md)
