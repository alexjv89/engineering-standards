# One Change Driver Per Artifact

Every doc, spec, or note should have exactly one reason to change.

## Principle

When deciding whether two pieces of content belong in the same artifact, do not ask "are they about the same topic?" Ask: **what causes each to change, and is it the same thing?**

- If two artifacts always change together → they have one change driver → merge them.
- If one artifact changes for two unrelated reasons → it has two change drivers → split it.

**Why**: An artifact that co-changes with the code it describes is not an anchor — it is a parallel codebase. Anchors only work if their rate of change is slower than what they anchor.

## How this differs from MECE and Single Idea Per Note

- **MECE** asks: do these items overlap in topic? (Topic boundaries.)
- **Single Idea Per Note** asks: does this note explain one concept? (Concept boundaries.)
- **One Change Driver** asks: do these items move for the same reason? (Time / rate-of-change boundaries.)

Two artifacts can be MECE and single-idea and still be wrong — if they always change together they should be one, and if either changes for two reasons it should be two.

## Examples

**Bad (two change drivers in one artifact)**:

❌ `pipeline-design.md` covering both "what the customer does manually today" *and* "how our automation implements it."

The manual process changes when the customer changes how they work. The automation design changes when we learn from the build. Bundling them means every code refactor invites edits to the customer's process narrative, which then drifts from reality.

✅ Split into:
- `manual-process.md` — changes only when the customer changes their workflow.
- `automation-design.md` — changes as we learn from implementation.

**Bad (one change driver split across artifacts)**:

❌ `api-error-codes.md` and `api-error-messages.md` as separate artifacts when codes and messages always change together for the same reason (an error case is added or renamed).

✅ Merge into `api-errors.md`.

## Applying the principle

Before writing or editing a doc, ask:

1. **What triggers an edit to this artifact?** Name the change driver in one sentence ("the customer changes their AP workflow", "we add a new step type", "a finance policy changes").
2. **Does any other artifact share this trigger?** If yes, they may want to merge.
3. **Does this artifact have more than one trigger?** If yes, it wants to split.

A useful test: if you cannot finish the sentence "this doc changes when ___" without using *and*, it has more than one change driver.

## Why this matters for compounding work

Specs and design docs only compound when they stop being rewritten. An artifact that is rewritten on every code change is recording the present, not anchoring it. Separating by change driver creates a layered set in which slow-changing artifacts (context, jobs to be done, business rules) become real anchors that fast-changing artifacts (implementation) can be traced back to.

## Related Notes

- [MECE](/principles/documentation/mece.md)
- [Single Idea Per Note](/principles/documentation/single-idea-per-note.md)
- [Actual Patterns Not Aspirational](/principles/documentation/actual-patterns-not-aspirational.md)
