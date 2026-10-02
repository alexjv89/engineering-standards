# Try It to Decide

When a decision depends on how something will behave, build the smallest version and look. Don't try to settle it on paper.

## The Principle

Some questions can be answered by thinking: naming, data ownership, which of two known APIs fits. Others can only be answered by seeing: which folder layout an accountant actually finds useful, how often a classifier gets a document type wrong, whether a batch size is too big. Treating the second kind like the first leads to analysis paralysis. Options multiply, each answer opens new questions, and nothing ships, because the information needed to choose doesn't exist yet.

When you hit an open question, ask what kind it is:

- **Answerable now**: the facts are available. Decide.
- **Answerable by trying**: the facts appear once something runs. Pick a reasonable default, build the smallest thing that exercises it, and look at the result.
- **Not blocking**: the next step doesn't depend on it. Leave it open and write it down.

Only the first kind belongs in a design discussion. The second gets a default and an experiment. The third gets a line in a list.

## Why It Matters

- **Evidence beats speculation.** Ten minutes of output from a real run answers questions an hour of discussion can't, and it often shows that the question that seemed important doesn't matter.
- **Open decisions stall work.** Every question raised before building is one the builder waits on. A list of five questions blocks progress even if four of them don't affect the next step.
- **Most early choices are cheap to change.** A folder name, a default, a threshold: these are two-way doors. Getting them wrong costs a rename, not a rewrite.
- **Momentum compounds.** A running version gives every later decision something concrete to point at.

## Anti-Pattern

Asked to make an intake step delete files after registering them, the reply comes back with three decisions to make first: where to file document types that have no extractor yet, where the final filed tree should live, and what order to ship the pieces in. None of them blocks the requested change, and the first can only be answered after seeing which documents actually arrive and how they get classified.

## Correct Approach

Make the requested change with sensible defaults. Leave unclassified documents where they already sit; they're visible in their bins, and someone will deal with them when it matters. Run the line on real data. The questions that are still open afterwards are now concrete, with specific files and counts to point at, and so they are quick to answer.

## When to Decide Up Front

Settle it before building when:

- **It's a one-way door.** Deleting data, publishing an API others will depend on, or committing money is hard to undo, so think first.
- **Trying is expensive.** If the smallest experiment takes weeks or real customer impact, more thinking up front is cheaper.
- **It blocks the very next step.** You can't write the code without an answer. Ask exactly that one question.

## Smell Test

1. **"Would running something tell us the answer?"** If yes, run it instead of debating.
2. **"Does the next step actually depend on this?"** If not, write it down and move on.
3. **"Is this cheap to change later?"** Then pick a default now.
4. **"How many questions am I asking before anything runs?"** More than one is a sign to start building.

## Related Notes

- [Solve Problems You Have Seen](/principles/solve-problems-you-have-seen.md) — the same instinct applied to solutions: note imagined problems, solve real ones
- [Decision Records](/git-workflow/decision-records.md) — one-way vs two-way doors; record a decision once trying has settled it
- [Emergent Structure](/principles/documentation/structure/emergent-structure.md) — structure that appears from use rather than being designed up front
