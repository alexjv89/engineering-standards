# Knowledge Base vs. Activity Report

A knowledge-base document records **timeless facts** — what a thing is, how it works, how to recognize or reproduce or remediate a *class* of problem. An activity report records **what happened on a specific run** — what we did, what we found on this account, whether this instance is fixed yet. They have different change drivers (see [One Change Driver](/principles/documentation/structure/one-change-driver.md)), so they belong in different homes.

## The Boundary

| Knowledge base (docs) | Activity report (records) |
|---|---|
| Changes when we **learn** something new about the world | Changes when we **do** a run, or an instance's status changes |
| The *class* of issue: what it is, how to spot it, how to reproduce, its impact, how it's remediated | The *instances*: which accounts/systems hit it, on what date, fixed or still open |
| One illustrative example is fine (pedagogy) | The running tally of every instance and its status |
| Trains the next engineer / accountant / LLM | Tells today's reader the state of the work |
| Lives with the domain (standards repo, `docs/`) | Lives with the work (per-project/per-client report files, logs, trackers) |

**The test**: does this sentence change when *the world changes* or when *we run again*? "This bug is caused by page-boundary pagination" is knowledge. "Fixed on account X, 2026-07-21" is a report.

## Anti-Pattern: A Status Section Inside a Knowledge Note

```
docs/issues/missing-transaction.md
  ## Summary        ← knowledge
  ## Reproduce      ← knowledge
  ## Impact         ← knowledge
  ## Status
     Fixed 2026-07-21 on HDFC-2719; still open on ICICI-3000  ← REPORT — wrong home
```

The `Status` section drags a timeless note into being rewritten every time an instance's status changes. Now the "knowledge base" is stale the moment someone applies a fix and forgets to edit the note — and it will be forgotten, because the fix happens in the report workflow, not the doc. Strip the status; let the knowledge note document the *pattern*, and let the report track the *instances*.

The reverse is just as wrong: a run report that re-explains the whole methodology inline instead of linking to the standard is duplicating knowledge that will drift.

## Why It Matters

A knowledge note only compounds if it stops being rewritten (see [One Change Driver](/principles/documentation/structure/one-change-driver.md)). Bundling per-run status guarantees churn: the note co-changes with the activity and stops being an anchor. Separating the two lets the slow-changing knowledge accumulate trust while the fast-changing report stays honest about the present.

## Applying It

- Writing a doc and about to add a date, an account id, or a "fixed/open" state? That's a report line — move it to the report side and leave (at most) a generic pointer: "instances and their status are tracked in the run reports."
- Writing a report and about to explain *why* something is a known pattern? Link to the knowledge note instead of restating it.
- A knowledge note may name a real example for teaching; it must not maintain the census of instances.

## Related Notes
- [One Change Driver](/principles/documentation/structure/one-change-driver.md)
- [Changelog, Not Inline Revision History](/principles/documentation/structure/changelog-not-inline-history.md) — where a note's own revision history goes instead
- [Actual Patterns Not Aspirational](/principles/documentation/actual-patterns-not-aspirational.md)
- [Single Idea Per Note](/principles/documentation/structure/single-idea-per-note.md)
- [Skill Writing Principles](/principles/skills.md) — the same state-vs-docs split, for skills
- [Working Docs](https://github.com/finopsbricks/fde-handbook/blob/main/working-docs/README.md) — how to shape the *report* side (status / to-do / log across nested scopes)
