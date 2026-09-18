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
- **Reversibility:** Two-way door (cheap to reverse)
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

## Review

[Left blank at first. Come back after the decision has played out: what actually
happened vs. what "Why" predicted, and what you'd decide differently.]

## Related

- `docs/wip/thing.md` — the exploration this concludes
```

**Title**: `# NNNN — <conclusion>`, em-dash, never a colon. State the conclusion, not the topic, and put the negative case in it — `No offline writes.` / `blocked, not coordinated`.

**Metadata**: `- **Key:** value`. `Status:` first, always `Accepted — YYYY-MM-DD`; add `(recorded retroactively; the choice was made YYYY-MM-DD)` for a past decision. Optional: `Reversibility:`, `Scope:`, `Builds on:`, `Governs:`, `Supersedes:`.

**Reversibility**: one-way door (hard/costly to reverse) or two-way door (cheap/fast to reverse). Naming it up front sizes the rigor the ruling deserved — a two-way door does not need the same steelmanning as a one-way one, and a reader deciding whether to relitigate needs to know which they're touching.

**Sections**: `## Decision` and `## Why` are required. Rename `## Why` → `## Why not` when the ruling is a refusal. Subdivide a long `## Why` with H3s that are assertions, not labels.

## What Makes These Worth Writing

Write one when someone would otherwise relitigate the decision. Not every choice earns a record.

- **Cite every claim** — commit SHA, file path, measurement, or a dated confirmation.
- **Steelman the opposition**, and say which argument is strongest. It is what will reopen this.
- **Record unresolved costs as blockers**, in bold, rather than smoothing them over.
- **Say why the record exists** when the ruling looks like an inconsistency someone will later "fix".

## Review closes the loop

`Revisit when` names the triggers that reopen a decision. `## Review` is the backward-looking counterpart: once the decision has played out, come back and record what actually happened against what `## Why` predicted. It is optional and left blank at first — but it is what turns a wall of decision *announcements* into a record you can learn from, because the only way to decide better is to check predictions against outcomes.

## Immutability

A record is a point-in-time ruling. Do not edit it to reflect a later change of mind — write a new record that supersedes it, and add `**Supersedes:**` to the new one. Editing is for facts that were wrong when written.

The one sanctioned later addition is `## Review` — it *appends* the outcome, it does not rewrite the ruling. A changed mind is a new record; a played-out prediction is a Review line.

## Related Notes

- [WIP Files](/git-workflow/wip-files.md) — the exploration a decision record concludes
- [Retrospectives](/git-workflow/retrospectives.md) — reflects on the work; a decision record fixes a rule
- [Changelog](/git-workflow/changelog.md) — records *what* changed; a decision record records *why*
- [Knowledge Base vs. Activity Report](/principles/documentation/structure/knowledge-vs-reports.md) — a decision is knowledge, not status
- [Actual Patterns Not Aspirational](/principles/documentation/actual-patterns-not-aspirational.md) — record what was decided, not what was hoped
