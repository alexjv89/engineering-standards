# Knowledge Notes Carry No History

A knowledge note states what is true now. It never mentions, cites or links to the history of how we got there. History lives in logs, and logs link to knowledge notes, never the other way round.

## Two Kinds of Documentation

| | Knowledge note | Log |
|---|---|---|
| **Is** | Reference material: the accumulated knowledge, as it stands today | History: what happened, what was decided, what was tried |
| **Examples** | A standard, a how-it-works note, a published guideline | Decision records, WIP files, studies, changelogs, run reports, retrospectives |
| **Read by** | Anyone who needs to *use* the knowledge | Someone who wants to *question* the knowledge or see how it came to be |
| **Lives in** | The published or reference location (`content/`, a standards folder, `docs/` topic notes) | `docs/decisions/`, `docs/wip/`, `docs/study/`, `CHANGELOG.md`, report folders |

"Knowledge note" and "reference note" mean the same thing here. This note uses *knowledge note* throughout.

## Why

Documentation is accumulated knowledge used as reference. Its reader came to get the value **right away**: apply the rule, build the thing, answer the question. Most readers are not questioning the knowledge; they are using it.

History serves only the reader who *is* questioning it. Put it in the knowledge note and every other reader pays for it: they read past ratings, quotes, dates, "decision 0004" citations and "originally X" asides to reach the rule, and have to work out which sentences are the current rule and which are the story. Keeping the two apart makes the knowledge note fast to consume, and leaves no doubt about what is current.

The reader who wants to question a rule knows where to look: the logs. They don't need the knowledge note to send them there.

## The Rule

A knowledge note contains no:

- **Citations of logs**: "decision `0001`", "see study 02", links to WIPs, changelogs or decision records, "Related: 0003 — …".
- **Evidence from how the rule was reached**: who rated or said what, quotes from the brief, audit counts from a dated snapshot.
- **Dates and status**: "as of 2026-10-20", "proposed", "work in progress", "will be revisited later", "recent changes".
- **Revision history**: "originally X, now Y" (see [Changelog, Not Inline Revision History](/principles/documentation/structure/changelog-not-inline-history.md)).
- **Unsettled material**: explorations, drafts and candidates are WIPs, not knowledge notes, even when they render nicely.

A knowledge note **may** keep a `## Why` section, written as the timeless reason the rule holds: true today, readable without knowing its history.

## Anti-Pattern

```markdown
❌ ## Why

The sites Alex loved all show the thing (Linear, Attio, Raycast). Those rated
Meh sell atmosphere (CRED, Superhuman). [Study 02](…/docs/study/02-landing-taste.md).

## Related Notes
- [0001 — Landing pages follow five taste principles](/decisions/0001-…)
```

```markdown
✅ ## Why

Seeing the work answers "what is this?" faster than any headline, so the
visitor understands the product without thinking. Atmosphere leaves them
to guess what the product does.
```

The ratings, quotes and the study link stay in the decision record and the study, which link to this note.

## Direction of Links

- **Log → knowledge note:** yes. A decision record names the notes it governs; a WIP links the notes it changes; a changelog entry names the note that changed.
- **Knowledge note → log:** no.
- **Knowledge note → knowledge note:** yes, as usual (see [Cross-Linking Notes](/principles/documentation/linking/cross-linking-notes.md)).

## Check

- Read the note as someone who only wants to apply it. Is every sentence the current rule, how to apply it, or why it holds today?
- Does it contain a date, a decision number, a study, a person's rating or quote, or a status word? Move it to the log.
- Would the note read the same if it were written from scratch today, with no memory of how we got here?

## Related Notes

- [Knowledge Base vs. Activity Report](/principles/documentation/structure/knowledge-vs-reports.md): the same split for per-run status
- [Changelog, Not Inline Revision History](/principles/documentation/structure/changelog-not-inline-history.md): the same split for a note's own edits
- [One Change Driver Per Artifact](/principles/documentation/structure/one-change-driver.md): why knowledge and history belong in different artifacts
- [Decision Records](/git-workflow/decision-records.md) and [WIP Files](/git-workflow/wip-files.md): where the history goes
