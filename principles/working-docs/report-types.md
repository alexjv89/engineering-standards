# Working-Doc Report Types: Status, To-do, Log

A working doc is exactly one of three types. Mixing two in one file gives it two change drivers (see [One Change Driver](/principles/documentation/one-change-driver.md)) and it churns.

| Type | Answers | Shape | Changes when |
|---|---|---|---|
| **status** | "Where are things *now*?" | snapshot; overwrite in place | the current state changes |
| **to-do** | "What must a human *do*?" | checklist; items leave when done | work is added or completed |
| **log** | "What changed, *when*?" | append-only, dated, newest first | an event happens |

## Distinguishing them

- **Status vs log** is the most-confused pair. Status is the *present*; log is the *past*. A status file that carries "was deleted on…" has smuggled in a log entry — see [Status Has No Tombstones](/principles/working-docs/status-has-no-tombstones.md). For history, read the log or the status file's own version history — never make status derive from the log.
- **To-do vs log**: a to-do is a *desired future*; once done it becomes a *past event* — remove it from the to-do (it leaves) and append a line to the log.

## Generated vs hand-written

Type doesn't strictly dictate this, but in practice **status is almost always generated** (derived from finer-scope data), while **to-do and log are hand-written** (a human or agent asserts them, since no computation can derive "what someone still must do" or "what happened"). See [Generate Higher Scopes From Lower](/principles/working-docs/generated-rollups.md).

## Related Notes
- [Working Docs](/principles/working-docs/README.md)
- [One Change Driver Per Artifact](/principles/documentation/one-change-driver.md)
- [Status Has No Tombstones](/principles/working-docs/status-has-no-tombstones.md)
