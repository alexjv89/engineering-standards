# Jobs: A Bounded Piece of Work

A **job** is one discrete piece of work with a beginning and an end — an investigation, a tie-out, a data cleanup, a period close. It is a **mutable, checklist-first** working doc that guides that one effort from start to finish, then closes. It is the operational-work counterpart to an engineer's [WIP file](/git-workflow/wip-files.md).

## Standing vs bounded

The other working docs — [status, to-do, log](/principles/working-docs/report-types.md) — are **standing**: they track an operation that never ends (an account is reconciled this month, and again next month). A job is **bounded**: it opens, gets worked, and closes. That is a different axis, not a fourth type.

| | Standing docs | Job |
|---|---|---|
| Answers | "where are we / what's left / what happened" on an ongoing operation | "how do I get this one piece of work done" |
| Lifespan | permanent, per scope | opens → worked → closes |
| Shape | fixed (snapshot / checklist / append-only) | free — starts as a checklist, edited as you learn |
| Ends by | never | deletion, once its lasting parts have flowed out |

## Shape

Start it as a **checklist** — the plan as you understand it now. As you work, edit freely: add steps you didn't foresee, drop the ones that turn out moot, check off what's done, and record decisions and dead-ends inline. A typical job carries a one-line status, why the work is needed, the data sources, the plan as phased checkboxes, and links to its outputs (a findings report, derived tables, raw evidence).

Reuse the WIP conventions for status and phase markers (`IN PROGRESS` / `COMPLETE` / `BLOCKED`; ✅ 🔄 ❌) — a job and a WIP file are the same shape aimed at different work.

## Where it lives

On the **reports** side, under the client/org it serves: `clients/<org>/jobs/<name>.md`. Not in `docs/` — that is [knowledge, not reports](/principles/documentation/knowledge-vs-reports.md). Not in an engineering `docs/wip/` — that is for code changes; an accounting effort is neither code nor knowledge.

## A job is not a to-do

A [to-do](/principles/working-docs/report-types.md) is a single desired change, sitting in a standing list; it leaves that list when done. A job is a whole bounded *effort* that may run many steps, hold working notes, and produce a findings report. A job can *spawn* to-do items (app changes it uncovers); it is not itself one.

## Closing a job

When the work is done, its durable parts flow **out** to where they belong, and the job itself is deleted — it was scaffolding, not the permanent home of any fact:

- a lasting *outcome* → a line in the relevant [log](/principles/working-docs/log-entry-format.md)
- an app change still owed → a [to-do](/principles/working-docs/report-types.md) item
- something learned about a *class* of problem → a knowledge note ([knowledge vs. reports](/principles/documentation/knowledge-vs-reports.md))
- the findings themselves → kept as the effort's output artifact, referenced from the log

Don't let finished jobs accumulate — like WIP files, delete them once distilled.

## Related Notes
- [Working Docs](/principles/working-docs/README.md)
- [Working-Doc Report Types](/principles/working-docs/report-types.md) — the standing types a job feeds into
- [WIP Files](/git-workflow/wip-files.md) — the engineering counterpart
- [Keep Derivations Out of Operations](/principles/working-docs/derivations-stay-out-of-operations.md)
- [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md)
