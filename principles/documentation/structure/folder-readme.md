# Folder README, Not Index

An optional folder-level note for orientation, not a file map.

## Principle

Some folders benefit from a few sentences explaining what they're for — enough for someone unfamiliar with the folder to get their bearings before diving in. Call this file `readme.md`, lowercase, at the folder root.

**Optional**: skip it if the folder's purpose is already obvious from its name and the atomic notes inside. Don't create one preemptively.

**Minimal**: a paragraph or two of orientation — not a file listing, not a relationship map. Those already live elsewhere: `ls` shows what files exist, [Meaningful Titles](/principles/documentation/voice/meaningful-titles.md) makes filenames self-describing, and each note's own `## Related` section (see [Cross-Linking Notes](/principles/documentation/linking/cross-linking-notes.md)) captures how notes connect.

## Why Not `index.md`

`index.md` invites scope creep: the name implies "index of contents," which pulls toward listing every file and mapping every relationship — both already covered elsewhere. That duplication has a real cost: two places to update when a note is renamed or a relationship changes, and they drift out of sync (a stale index reads as authoritative, which is worse than no index).

`readme.md` doesn't carry that expectation — it reads as "read me first if you need context," not "here is the complete map."

## When to Add One

Add a `readme.md` when a folder holds enough atomic notes (loosely, [Emergent Structure](/principles/documentation/structure/emergent-structure.md)'s 8+ threshold for a subfolder) that a newcomer can't tell what the folder is about from its name and directory listing alone.

Don't add one to a folder with a handful of self-explanatory notes — the folder name plus `ls` already does the job.

## Good Example

```markdown
# Ivalua Non-Sourcing Spike

A focused spike investigating what master data Ivalua needs for
non-sourcing POs, and what actually happens in SAP for that kind
of spend today.

## Related
- [PO Types](../po-types/readme.md)
```

Two sentences of orientation, one cross-link. Nothing here duplicates a directory listing or an atomic note's own relationships.

## Bad Example

```markdown
# Ivalua Non-Sourcing Spike

...

## Notes in this set
- [Ivalua Item and Service Master](ivalua-item-service-master.md)
- [SAP Non-Sourcing PO Pattern](sap-non-sourcing-po-pattern.md)
  - [Non-Sourcing PO Lines Are Free Text](non-sourcing-lines-are-free-text.md)
  - ...
```

This is an index, not a readme — it re-lists what `ls` already shows, and re-states relationships that belong in each note's own `## Related` section.

## Related Notes
- [Emergent Structure](/principles/documentation/structure/emergent-structure.md)
- [Discovery Patterns](/principles/documentation/structure/discovery-patterns.md)
- [Cross-Linking Notes](/principles/documentation/linking/cross-linking-notes.md)
- [Meaningful Titles](/principles/documentation/voice/meaningful-titles.md)
