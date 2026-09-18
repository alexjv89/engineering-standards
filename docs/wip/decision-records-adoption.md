# Decision-Records Standard — Cross-Repo Adoption

## Status: COMPLETE

Rolled the [decision-records standard](/git-workflow/decision-records.md) out across the finopsbricks
repos: one convention for every decision, technical or business — `docs/decisions/` (root `decisions/`
in a docs-only repo) with unique per-repo `NNNN`. All structural work (folder names, ID collisions,
folding in the founders' business log) is done and pushed. Only going-forward section-heading
convergence remains, which is a review-time policy rather than a task — this WIP can be deleted once
that's acknowledged.

---

## Problem Statement

Six repos ran decision logs before the standard existed, in diverging shapes. Now that
`git-workflow/decision-records.md` is merged, the live logs should line up with it on the dimensions
that matter — folder name and the uniqueness of the `NNNN` identity — without violating the
standard's own immutability rule by rewriting already-accepted records.

## Survey (as of 2026-09-18)

| Repo | Folder | Records | Structural status |
|---|---|---|---|
| `apps/statements` | ~~`docs/decision-log/`~~ → `docs/decisions/` | 0001–0003 | ✅ **Fixed** — renamed folder, resolved duplicate `0002` |
| `apps/ledger` | `docs/decisions/` | 0001–0005 | ✅ Conformant |
| `apps/orchestrator` | `docs/decisions/` | 0001–0003 | ✅ Conformant |
| `cli/fob-cli` | `docs/decisions/` | 0001 | ✅ Conformant |
| `cli/fob-email` | `docs/decisions/` | 0001–0002 | ✅ Conformant |
| `ops/company-ops` | ~~`decision-logs/` (`YYYY-MM-DD-`)~~ → `decisions/` (`NNNN-`) | 1 | ✅ **Fixed** — a decision is a decision whether business or technical; folded into the one standard (folder + `NNNN` + Status/Decision/Why, keeping Decider(s)/Reversibility/Review) |
| `workers/worker-agilitas` | `docs/.../decision-rules/` | — | ⏸️ **Out of scope** — domain P2P-mapping rules, not decision records. |

Structural conformance is now complete across all six decision logs (five code repos on
`docs/decisions/`, plus `company-ops` on root `decisions/`): all use the standard folder, all `NNNN`
are unique, no collisions remain. Business and technical decisions now share one convention — see the
["technical or business" clause](/git-workflow/decision-records.md) in the standard.

## Remaining: soft heading convergence (going-forward only)

Existing accepted records use section headings that predate the standard's rulings:

- `## Consequences` (ledger, fob-email) — standard splits this into `## Honest costs` (costs only)
  with benefits/reasoning folded into `## Why`.
- `## Not foreclosed` / `## Still open` (orchestrator, fob-cli) — standard prefers `## Revisit when`
  (falsifiable triggers).
- `## Context` + `Date:` metadata (statements) — standard leads with `## Decision` and embeds the
  date in `Status:`.

**Decision: do not mass-edit these.** The standard says a record is an immutable point-in-time ruling;
reformatting accepted records is churn that risks mangling their meaning for cosmetic uniformity. New
records in every repo follow the standard; old ones converge only if reopened/superseded for a real
reason. This is the [present-tense / one-change-driver](/principles/documentation/structure/one-change-driver.md)
logic applied to the records themselves.

## Implementation Phases

### Phase 1: Survey ✅
- [x] Enumerate every decision-log folder across finopsbricks
- [x] Classify architectural (in scope) vs business/domain (out of scope)
- [x] Detect `NNNN` collisions and folder-name divergences

### Phase 2: Structural fixes ✅
- [x] `apps/statements`: rename `docs/decision-log/` → `docs/decisions/`
- [x] `apps/statements`: resolve duplicate `0002` (umami keeps 0002; decouple-ocr → 0003)
- [x] `apps/statements`: update inbound refs (`ocr-extraction.md`, 2 WIPs); committed + pushed
- [x] Confirm ledger / orchestrator / fob-cli / fob-email already conformant (no action)
- [x] `ops/company-ops`: fold the founders' log into the one standard — `decision-logs/` → root
  `decisions/`, date filename → `0001-`, entry reshaped to Status/Decision/Why, `_TEMPLATE`/`README`
  aligned, 6 inbound refs fixed; committed + pushed
- [x] Broaden the standard to say a decision is a decision whether technical or business, add
  optional `Decider(s)`, and document root `decisions/` for docs-only repos

### Phase 3: Going-forward convergence ❌ (no active work — policy, not a task list)
- [ ] New records follow the standard headings; reviewers flag divergence at write time
- Section-heading convergence on the already-accepted code-repo records (`Consequences` →
  `Honest costs`, `Not foreclosed`/`Still open` → `Revisit when`) is deliberately *not* done —
  reformatting immutable records is churn; they converge only if reopened for a real reason.

## Related Files

- `git-workflow/decision-records.md` — the standard being adopted
- `git-workflow/changelog.md` — the *what*-changed sibling
- Fixed: `apps/statements.finopsbricks.com/docs/decisions/` (commit `1fb578f7`)
