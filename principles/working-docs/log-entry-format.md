# Log Entry Format: Nested Bullets, Newest First

A log entry is a **bold headline** followed by indented sub-bullets of short phrases — not a prose paragraph. Newest entry first.

## Shape

```markdown
- **2026-07-21 — Deleted duplicate account `ICICI Bank 0006`**
  - Accidental double-registration of the same card
  - All 273 transactions were misattributed copies of sibling `ICICI 0006`
  - Deleted all 273 rows + the account record itself
  - Pattern: [cross-account misattribution](/…)
```

## Why not prose

The headline carries the *what* and *when*; a reader scans headlines and drills into sub-bullets only for the one entry they care about. A paragraph forces them to read the whole block to extract the same facts — see [Brevity](/principles/documentation/brevity.md) (structure = scannable). Phrases beat sentences: drop filler, keep the fact.

## Related Notes
- [Working-Doc Report Types](/principles/working-docs/report-types.md)
- [Brevity](/principles/documentation/brevity.md)
