# Solve Problems You Have Seen

Build for the problems in front of you. A problem you can imagine but haven't hit yet gets a note, not a solution.

## The Principle

While designing, it's easy to picture failure modes: a crash at the wrong moment, a file that's too big, a folder someone wipes by mistake, a vendor with two names. Each one feels responsible to guard against. But every guard is code to write, read, test and keep correct, and it is shaped by a guess about a problem whose real form you don't know yet.

So split what you notice into two piles:

- **Seen**: it happened, or the change you are making right now directly causes it. Fix it.
- **Imagined**: it could happen. Write it down in a line (a known-limitations list, a comment, a WIP note) and move on.

When an imagined problem shows up for real, you solve it with evidence: how often it happens, what it actually looks like, what it costs. That solution will be smaller and better aimed than the one you would have guessed.

This is not a licence to ignore risk. Noticing is good; *solving* early is the cost. Keep the list, and look at it when something breaks.

## Why It Matters

- **Guesses miss.** A safeguard designed before the failure exists fits an imagined failure. The real one usually differs in shape, frequency, or where it bites.
- **Guards cost on every read.** Each defensive branch, extra state, or config knob is something the next reader must understand before they can change the code, whether or not the problem ever happens.
- **Most imagined problems never arrive.** Effort spent on them is effort not spent on the problems users actually hit.
- **Simple systems are easier to fix later.** Adding a targeted fix to small code is cheap. Removing an unneeded safeguard from tangled code is not.

## Anti-Pattern

A step that drains an intake folder is being designed. Before the first version runs, the design grows: a rejects folder for unreadable files, a vendor-slug registry keyed by tax ID so filenames stay stable, collision suffixes for re-scanned invoices, a policy for where unclassified documents get filed, and a warning that scratch folders could be deleted by mistake.

None of these problems has been seen. Each adds a decision, code, and a test, and together they delay the one change that was asked for.

## Correct Approach

Make the asked-for change: register each file, then delete it from the folder. Fix only what that change itself causes. Deleting the original means the registry entry must be written *after* the workpiece is safely created, or a crash loses the file. List the rest as one-line known limitations, and revisit them when a real run shows which ones matter.

## When to Solve It Now

Act before seeing the problem when at least one is true:

- **Your change causes it.** A risk your change introduces directly (deleting a file before it's safely copied) is part of the change, not a future problem.
- **It's a one-way door.** Data loss, money moved, a message sent, a public contract published: things that can't be undone once they happen.
- **The fix is trivial and local.** Reordering two lines costs less than writing the note about why you didn't.

## Smell Test

1. **"Have I seen this happen, or am I picturing it?"** If picturing, write it down instead.
2. **"Does my change cause this, or does it already exist?"** Fix what you cause; list what already exists.
3. **"If this happens, can we undo it?"** If yes, wait for it.
4. **"Would a one-line note do the job for now?"** Usually it does.

## Related Notes

- [Try It to Decide](/principles/try-it-to-decide.md) — the same instinct applied to decisions: don't settle on paper what running the thing will answer
- [Abstraction Must Earn Its Keep](/principles/abstraction-earns-its-keep.md) — the same rule for indirection: wait for the second real use
- [Decision Records](/git-workflow/decision-records.md) — one-way vs two-way doors, which sizes how much care a choice deserves
