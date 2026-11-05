# Where a Piece of Code Lives

This note owns one decision: whether code stays inline, becomes a named function, moves to a file beside its caller, or moves to a central folder such as `utils/`. Other notes link here rather than deciding it themselves.

## The Ladder

Start at the top. Move down a rung only when that rung's condition is true today.

| Rung | Where | Move here when |
|------|-------|----------------|
| 1 | Inline | Default |
| 2 | Named function, same file | The name says more than the body, the same logic repeats in the file, the block runs past ~20 lines, or it needs its own tests |
| 3 | Separate file, beside its caller | Two files in the same folder use it, or it is presentation (a template or report layout) |
| 4 | Separate file, central folder | Callers in two different folders use it |

Rung 2 is about reading: does this block deserve a name? Rungs 3 and 4 are about sharing: who uses it, today? Presentation is the one exception, because it gets its own file even with one caller.

## Rules

- **Count real callers.** A test is not a caller, and neither is a caller you expect later.
- **Tests never move code to a new file.** To test a function directly, export it from the file it lives in.
- **Move when the condition arrives.** When a second folder needs the function, move it then. A move is a short edit; a wrong guess stays in the way.
- **Move back up too.** When a central file drops to one caller, it goes back beside that caller.

## Example

A step uploads a file by calling an external CLI. The wrapper parses JSON and sorts errors into categories.

```
src/steps/SI1__upload/SI1_01_upload.js     # rung 2: wrapper is a function in the step
src/steps/SI1__upload/report.md            # rung 3: presentation stays out of the step
src/utils/statement-rename-script.js       # rung 4: called by BK-SR2 and BK-SR3
```

Layout gets rung 3 because of [Separate Presentation From Business Logic](/principles/scripting/separate-presentation-from-logic.md). A wrapper with one caller stays at rung 2, however long it is.

## Check

1. Which rung is this code on, and which condition put it there?
2. Is that condition true today, without counting tests or expected callers?
3. Does any file in the central folder have only one caller?

## Related Notes

- [Abstraction Must Earn Its Keep](/principles/abstraction-earns-its-keep.md) — the principle behind the ladder
- [Solve Problems You Have Seen](/principles/solve-problems-you-have-seen.md) — why a future caller doesn't count
- [Utility Modules](/architecture/modules/utility-modules.md) — how to arrange the central folder once code is there
- [Module Exports Pattern](/architecture/modules/module-exports-pattern.md) — how a module exports what it holds
