# Retrospectives

A retrospective is a reflective analysis of *completed* work — a work session or a range of commits —
that extracts what worked, what went wrong, and the concrete lessons, so the next effort improves. It
is the **"after" counterpart to a [WIP file](/git-workflow/wip-files.md)**: a WIP guides work in
flight; a retro looks back once it's done.

A retro is **not a log.** A log records *what happened*, neutrally. A retro is *analytical and
actionable* — it names the root cause and prescribes what to do differently.

## Two sources

A retrospective is written from one of two evidence bases:

| Source | Evidence | Written after |
|---|---|---|
| **Session** | a chat / work transcript | a long working session — design, investigation, a build |
| **Commit range** | `git log` + diffs over a span | a milestone, a sprint, or a merged effort |

Either way the discipline is identical: **anchor every claim in the evidence** — quote the actual
message or cite the commit. A retro written from memory drifts into platitudes.

## Location & naming

```
docs/
└── retros/
    ├── 2026-07-29-job-model-session.md      # session-based
    └── 2026-08-10-parser-migration.md       # commit-range-based
```

**Location**: `docs/retros/` — tracked in git, kept. **Naming**: `YYYY-MM-DD-<slug>.md`, dated by the
work it covers.

## Structure

```markdown
# Retro — [Title] ([date or commit range])

[One paragraph: what work this covers and where the evidence lives.]

## The through-line (root cause)
[The single tension or pattern behind most of what follows.
 A retro with no through-line is just a list of gripes.]

## What went well
[Wins, each with the specific that made it work — not "good collaboration".]

## What went wrong — regressions
[The meat. Each entry: the concrete moment (quote / commit), the model or goal
 it violated, and the fix. Group by cause when a pattern repeats.]

## Actionable checklist for next time
[Checkable do-this items, one per lesson. If it isn't checkable, it isn't a lesson.]

## Open threads
[Where the work stands, so the next session resumes without rediscovery.]

## References
[The WIP, the standards / mental models touched, the transcript or commit range.]
```

## Principles

- **Evidence-anchored.** Cite the moment — a quoted message, a commit hash, a file — never an
  unsourced claim.
- **Actionable over descriptive.** Every mistake ends in a checkable next-time rule. "We should
  communicate better" is not a lesson; "scope read-only before creating any folder structure" is.
- **Honest.** Record the wrong hypothesis and who caught it. A retro that lists only wins teaches
  nothing.
- **Find the through-line.** Cluster mistakes by root cause. One named pattern beats ten scattered
  complaints.
- **Altitude-aware.** A retro is *history*, not knowledge — keep the durable rules here only until
  they graduate (see Lifecycle).

## Lifecycle

Unlike a WIP (deleted when done) or a status (overwritten in place), a **retro is written once and
frozen** — a dated snapshot of learning, kept for the record.

1. **Write** it after the work, from the transcript or the commit range.
2. **Graduate the durable lessons.** A lesson that will recur belongs in a **standard** (what to do)
   or a **mental model** (how to see); the retro links to it. The retro keeps the *story*; the
   standard keeps the *rule*. Don't strand a reusable principle in a dated file.
3. **Keep** the retro. It is the evidence trail behind the standards it spawned.

## Best Practices

**DO:**
- Anchor claims in quotes or commits.
- End every mistake with a checkable fix.
- Name one through-line.
- Link durable lessons out to standards / mental models.

**DON'T:**
- Write from memory — read the transcript or the diffs.
- List only wins.
- Mutate a retro after the fact — write a new one; the old is a record.
- Let a durable rule live only in the retro.

## Related Notes
- [WIP Files](/git-workflow/wip-files.md) — the "during" counterpart; a retro reflects on the finished work a WIP guided
- [Commit Format](/git-workflow/commit-format.md) — commit-range retros read the history these produce
- [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md) — a retro is activity (history); its lessons graduate to knowledge
- [Jobs](https://github.com/finopsbricks/fde-handbook/blob/main/working-docs/jobs.md) — a retro often reflects on a completed job run
