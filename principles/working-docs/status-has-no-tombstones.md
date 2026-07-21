# Status Has No Tombstones

A status file shows only entities that **currently exist**. A deleted or merged-away entity is simply absent — no `🗑️ Deleted` row, no "(removed)" placeholder kept "so people can see it's gone."

## Why

A tombstone is a past *event* ("X was deleted on…") living in a *present-state* file — a log entry in the wrong home (see [Report Types](/principles/working-docs/report-types.md)). Keeping it means status is now partly derived from history, re-coupling the two things scope × type deliberately separates.

## Where "where did X go?" is answered

- The **log** — the event was recorded there when it happened.
- The status file's own **version history** (git) — the row was present, then a commit removed it.

Both are lossless and neither burdens the live status with graveyard rows.

## Not a tombstone

An entity that still *exists* but is inactive — out of scope, blocked, archived-but-present — is real current state. It stays, carrying that status. The rule is about *non-existent* things, not *inactive* ones.

## Related Notes
- [Working-Doc Report Types](/principles/working-docs/report-types.md)
- [Working Docs](/principles/working-docs/README.md)
