# Keep Derivations Out of the Operational Set

Some valuable artifacts are **derivations** of the working docs — a case-study narrative, a cross-cutting analysis, a formatted report. Because they are recomputable from status + log, they are **generated on demand**, not maintained as part of the running operation.

## Why

A maintained derivation is one more copy of facts already in status and log. It drifts the moment the source changes and the derivation isn't regenerated — and it usually isn't, because nothing in the run loop depends on it. Every run then pays to keep it current for no operational benefit.

## The test

Ask: *does the process need this to decide its next step?*
- **Yes** → it's a working doc (status / to-do / log); keep it live.
- **No — it's for a human who occasionally wants the story** → generate it when asked, from the working docs, and don't wire it into the run loop.

## Example

A reconciliation effort keeps status + to-do + log per scope. A per-account *case study* (the full diagnostic narrative) is **not** produced each run — it is generated on request from those records when someone wants the deep read.

## Related Notes
- [Working Docs](/principles/working-docs/README.md)
- [Generate Higher Scopes From Lower](/principles/working-docs/generated-rollups.md)
- [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md)
