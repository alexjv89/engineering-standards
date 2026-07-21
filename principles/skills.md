# Skill Writing Principles

A skill (`SKILL.md`) is a reusable playbook an LLM loads to perform a recurring task. Like a [slash command](/principles/slash-commands.md), it should **orchestrate, not educate** — but a skill has a longer life and accretes more, so two extra disciplines matter: **progressive disclosure** (link to depth, don't inline it) and **state vs. docs** (mutable run state never lives in the skill).

## Core Principle: A Skill Is Thin, Docs Are Deep

The skill holds the procedure and pointers. Everything else lives in a doc the skill links to, loaded only when the model follows the link.

| ✅ Skill Should Contain | ❌ Skill Should NOT Contain |
|------------------------|-----------------------------|
| One-paragraph purpose | The full methodology (link to it) |
| Usage / invocation | Reference catalogues (bug lists, issue indexes) |
| Ground rules / invariants | Per-run logs or progress trackers |
| The step-by-step playbook | Case studies / history |
| Links to the deep docs | Anything duplicated in a doc or the code |

**The test**: if a section would be equally at home in a standing doc, it belongs in the doc, and the skill should link to it.

## Progressive Disclosure

The skill is the index; the docs are the content. A fresh session reads a short skill, then opens only the doc relevant to the branch it's on. This keeps the always-loaded surface small and lets depth grow without bloating the skill.

- One canonical home per fact (**single source of truth**). If the same rule lives in the skill, a code comment, and a doc, the copies drift — the skill's copy drifts first and fastest.
- Reference the doc by path; don't paraphrase it. A paraphrase is a second copy.

## State vs. Docs

Anything that changes per run — a log of what's been processed, a status tracker, a checklist of remaining work — is **state**, not documentation. It belongs in a separate, deletable file next to the data it tracks, never interleaved into the skill.

- A tracker embedded in the skill can't be reset without editing the skill, and grows unbounded.
- Keep it scoped to what it tracks (e.g. per-org/per-project file), so one unit can be reset independently.

## Anti-Patterns

**Reference dump** — pasting a catalogue (known bugs, past findings, edge cases) into the skill. Move it to a doc; leave a one-line pointer:
```
❌  ## Known bug classes
    - 20 bullets restating docs/standards/....md
✅  Full catalogue: docs/standards/....md → "Known unknowns". Read it before assuming a finding is new.
```

**Run log in the skill** — a "Progress" section listing what's been done. That's state:
```
❌  ## Progress — ✅ account A (12/12) … ✅ account B …  (grows every run)
✅  Per-run status lives in <data-dir>/<scope>-log.md; read it first, append at the end.
```

**Triplicated knowledge** — the same rule in the skill, the code comments, and a doc. Pick one home; the other two link to it.

**God document** — one `SKILL.md` doing method + reference + tracker + history. Split by responsibility; the skill keeps only the playbook and links.

## Length Signal

A skill that keeps growing is usually absorbing docs or state. If it's past a page or two, look for a reference dump or a run log that should be a linked doc or a tracker file. Shrinking the skill while moving content to its right home is a net gain, not a loss — the depth is still there, just loaded on demand.

## Related Notes
- [Slash Command Writing Principles](/principles/slash-commands.md)
- [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md) — the same state-vs-docs split, generalized to all documentation
- [Hub-Spoke Doc Placement](/principles/documentation/hub-spoke-doc-placement.md)
- [One Change Driver](/principles/documentation/one-change-driver.md)
- [Single Idea Per Note](/principles/documentation/single-idea-per-note.md)
- [Document Density](/principles/documentation/document-density.md)
- [Brevity](/principles/documentation/brevity.md)
