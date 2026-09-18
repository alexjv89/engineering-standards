# Decision-Records Standard — Cross-Repo Adoption

## Status: IN PROGRESS (~90%)

Roll the [decision-records standard](/git-workflow/decision-records.md) out across the finopsbricks
repos: standardize on `docs/decisions/` + unique per-repo `NNNN`, and converge section headings. The
structural work (folder names, ID collisions) is done; only soft heading convergence remains, and it
happens going-forward rather than by editing immutable records.

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
| `ops/company-ops` | `decision-logs/` (`YYYY-MM-DD-`) | 1 | ⏸️ **Out of scope** — *business* decision log, not architectural rulings. Different convention by design (date-prefix, Reversibility, Review). It is the source of the Reversibility/Review ideas now in the standard. |
| `workers/worker-agilitas` | `docs/.../decision-rules/` | — | ⏸️ **Out of scope** — domain P2P-mapping rules, not decision records. |

Structural conformance across the five code repos is now complete: all use `docs/decisions/`, all
`NNNN` are unique, no collisions remain.

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

### Phase 3: Going-forward convergence ❌ (no active work — policy, not a task list)
- [ ] New records follow the standard headings; reviewers flag divergence at write time
- [ ] (Open question) Does `ops/company-ops` want the standard's `Reversibility`/`Review` fields
  formally referenced, or stay an independent business-decision convention? Raise with founders.

## Related Files

- `git-workflow/decision-records.md` — the standard being adopted
- `git-workflow/changelog.md` — the *what*-changed sibling
- Fixed: `apps/statements.finopsbricks.com/docs/decisions/` (commit `1fb578f7`)
