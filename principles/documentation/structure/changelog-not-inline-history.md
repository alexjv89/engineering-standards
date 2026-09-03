# Changelog, Not Inline Revision History

Record how a note's understanding changed in `CHANGELOG.md`, not inside the note.

## Principle

A knowledge note states current understanding. It should never say "originally X, now Y" or "this used to describe Z" — that's a report of how *the document* changed, not a fact about the *world*, and the two have different change drivers (see [Knowledge Base vs. Activity Report](/principles/documentation/structure/knowledge-vs-reports.md), [One Change Driver](/principles/documentation/structure/one-change-driver.md)).

Put that history in the repo's `CHANGELOG.md` instead — one line, under the change's own entry, linking to the note.

## Anti-Pattern

```markdown
❌ A master data object required for non-sourcing POs. Originally documented
as needed only for Non-Sourcing PO; per the fuller taxonomy, the two
sourcing-side service leaves presumably need it too, though unconfirmed.
```

```markdown
✅ A master data object required for service-spend POs. Confirmed for
Service — Non-Sourcing; unconfirmed for the two sourcing-side service
leaves (see Open Questions).
```

```markdown
CHANGELOG.md:
### Changed
- Service Master: broadened scope from non-sourcing-only to all three
  service leaves, per the PO Types taxonomy update.
```

## Why

- Git already keeps every past revision — a changelog just makes the *meaningful* ones scannable without diffing.
- A note that narrates its own history does two jobs (state the fact, log the edit) with two different change drivers, which is exactly what [One Change Driver](/principles/documentation/structure/one-change-driver.md) says to split.
- Narration ages badly: "originally X, now Y" reads fine the day it's written, but a second revision later it's "originally X, then Y, now Z" — the note grows a history section nobody asked for.

## When a Note Can Mention Its Own Past

Rarely, and only when the history itself is evidence a reader needs — e.g. "SAP's `DIEN` service material type has 97 records, 68 created in 2024" is a fact about the world, not documentation trivia. Test: would this sentence survive if the note were rewritten from scratch today, with no memory of previous drafts? If yes, keep it. If it only makes sense as "compared to what this note used to say," move it to `CHANGELOG.md`.

## Related Notes

- [Knowledge Base vs. Activity Report](/principles/documentation/structure/knowledge-vs-reports.md)
- [One Change Driver Per Artifact](/principles/documentation/structure/one-change-driver.md)
- [Simple, Sharp Voice](/principles/documentation/voice/simple-sharp-voice.md) — no meta-commentary
- [Brevity](/principles/documentation/voice/brevity.md)
