# Decision Records

Settled architectural rulings live in `docs/decisions/` as numbered, immutable records. A WIP explores; a decision record concludes.

## Location and Naming

```
docs/decisions/NNNN-kebab-case-slug.md
```

`NNNN` is a 4-digit, zero-padded, **per-repo sequential** number starting at `0001`. It is the decision's identity — cite it as "decision `0002`". Because the series is per-repo, `0001` in two repos are unrelated; cross-repo references need the repo name or a GitHub URL.

While a decision is still in flight it may carry a `D<n>` label in the WIP's working list. **Once pegged to an `NNNN`, drop the `D<n>`** — one identity, not two.

## Structure

```markdown
# 0002 — Tables are `accounts` / `transactions` / `entries`. Not `journals`.

- **Status:** Accepted — 2026-08-17
- **Scope:** the three core table names, FK columns, and API resource paths
- **Builds on:** [0001 — This app is a shadow ledger](./0001-shadow-ledger.md)

## Decision

[The ruling, present indicative, self-contained. A reader can stop here.]

## Why

[Reasoning. Name the rejected alternatives. Steelman the strongest opposing case.]

## Honest costs

[What this gives up. Mark deliberate acceptance: "Accepted." / "Judged acceptable:"]

## Revisit when

[Concrete falsifiable triggers, not "periodically".]

## Related

- `docs/wip/thing.md` — the exploration this concludes
```

**Title**: `# NNNN — <conclusion>`, em-dash, never a colon. State the conclusion, not the topic, and put the negative case in it — `No offline writes.` / `blocked, not coordinated`.

**Metadata**: `- **Key:** value`. `Status:` first, always `Accepted — YYYY-MM-DD`; add `(recorded retroactively; the choice was made YYYY-MM-DD)` for a past decision. Optional: `Scope:`, `Builds on:`, `Governs:`, `Supersedes:`.

**Sections**: `## Decision` and `## Why` are required. Rename `## Why` → `## Why not` when the ruling is a refusal. Subdivide a long `## Why` with H3s that are assertions, not labels.

## What Makes These Worth Writing

Write one when someone would otherwise relitigate the decision. Not every choice earns a record.

- **Cite every claim** — commit SHA, file path, measurement, or a dated confirmation.
- **Steelman the opposition**, and say which argument is strongest. It is what will reopen this.
- **Record unresolved costs as blockers**, in bold, rather than smoothing them over.
- **Say why the record exists** when the ruling looks like an inconsistency someone will later "fix".

## Immutability

A record is a point-in-time ruling. Do not edit it to reflect a later change of mind — write a new record that supersedes it, and add `**Supersedes:**` to the new one. Editing is for facts that were wrong when written.

## Related Notes

- [WIP Files](/git-workflow/wip-files.md) — the exploration a decision record concludes
- [Retrospectives](/git-workflow/retrospectives.md) — reflects on the work; a decision record fixes a rule
- [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md) — a decision is knowledge, not status
- [Actual Patterns Not Aspirational](/principles/documentation/actual-patterns-not-aspirational.md) — record what was decided, not what was hoped
