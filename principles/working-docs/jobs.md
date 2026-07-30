# Jobs: A Repeating Piece of Work

A **job** is a piece of operational work that **recurs** — a reconciliation, a period close, a tie-out run each cycle. It is a **mutable, checklist-first** working doc, owned by the doer, that guides one *run* of that work. It is the operational counterpart to an engineer's [WIP file](/git-workflow/wip-files.md) — the same shape, except a WIP is one-time and a **job repeats**.

## Definition + runs (not "bounded")

A job is not one-and-done. Split it:

- **Definition** — what the job is: its stages, method, and done-when. Stable, versioned, committed.
- **Run** — one cycle's execution: the checklist worked, findings, the doer's status claim. Each run resets the checklist and **is a commit**, so git history is the run log.

The *definition* is standing (permanent, per scope); each *run* is bounded (opens → worked → complete). A job is therefore **never deleted** — the next cycle re-runs it.

| | Standing report docs | Job |
|---|---|---|
| Answers | "where are we / what's left / what happened" | "how do I get this run of the work done" |
| Owned by | generated / asserted about the operation | the **doer** |
| Lifespan | permanent, per scope | definition persists; re-runs each cycle |
| Shape | fixed (snapshot / checklist / append-only) | free — a checklist, edited as you learn |

## Maker and checker: a job is not the record

A job and a [status report](/principles/working-docs/report-types.md) may describe the **same** state — deliberately. They are two independent sources:

- **Job (maker)** — the doer's claim: "I reconciled 10/10; here's the checklist and what I did."
- **Status (checker)** — an independent representation of system state, generated, never hand-edited.

The overlap is the **control**: the two must **converge**, and a real divergence carries a written explanation (in the job). One source can't audit itself — so a job keeps the doer's summary status *claim*, but never copies the report's row-by-row *detail* (that would drift).

## Shape

Start it as a **checklist** — the plan as you understand it now. As you work, edit freely: add steps you didn't foresee, drop the moot ones, check off what's done, record decisions and dead-ends inline. A run carries a one-line status (the doer's claim), why the work is needed, the data sources, the plan as phased checkboxes, and links to its outputs and to the independent report.

Reuse the WIP conventions for status and phase markers (`IN PROGRESS` / `COMPLETE` / `BLOCKED`; ✅ 🔄 ❌) — a job and a WIP file are the same shape aimed at different work.

## Jobs nest, and don't block each other

- **Nest.** A job decomposes into sub-jobs at finer scope (client/period → accounts → per-account checks). The job tree is the scope tree.
- **Non-blocking.** No job gates another. The doer does each to the **best of the data it has now**, and re-runs it better as more arrives. Priority between jobs is guidance, not a lock.

## Where it lives

On the **reports** side, under the client/org it serves: `clients/<org>/jobs/<name>.md`. Not in `docs/` — that is [knowledge, not reports](/principles/documentation/knowledge-vs-reports.md). Not in an engineering `docs/wip/` — that is for code changes; operational work is neither code nor knowledge.

## A job is not a to-do

A [to-do](/principles/working-docs/report-types.md) is a single desired change in a standing list; it leaves when done. A job is a whole *effort* that runs many steps, holds working notes, and produces outputs. A job can *spawn* to-do items (app changes it uncovers); it is not itself one.

## Completing a run

When a run is done: mark it `COMPLETE` and commit — that commit *is* the run record. Durable parts flow **out** to where they belong:

- a lasting *outcome* → a line in the relevant [log](/principles/working-docs/log-entry-format.md)
- an app change still owed → a [to-do](/principles/working-docs/report-types.md) item
- something learned about a *class* of problem → a knowledge note ([knowledge vs. reports](/principles/documentation/knowledge-vs-reports.md))

The job's definition and its worked instance **stay** — the next cycle resets the checklist and runs again. (Only a one-time effort — an engineering [WIP file](/git-workflow/wip-files.md) — is deleted when done.)

## Related Notes
- [Working Docs](/principles/working-docs/README.md)
- [Working-Doc Report Types](/principles/working-docs/report-types.md) — the standing report a job cross-checks against
- [WIP Files](/git-workflow/wip-files.md) — the one-time engineering counterpart
- [Retrospectives](/git-workflow/retrospectives.md) — reflect on completed runs
- [Keep Derivations Out of Operations](/principles/working-docs/derivations-stay-out-of-operations.md)
- [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md)
