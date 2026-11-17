# Emergent Structure

Structure emerges from content and connections, not rigid upfront planning.

## Principle

Let structure arise naturally from atomic notes and links rather than imposing organization beforehand.

**Why**: Rigid structures become obsolete. Emergent structures adapt naturally.

## How Structure Emerges

1. **Write atomic notes** without worrying about perfect organization
2. **Add cross-links** between related concepts
3. **Natural groups emerge** as similar notes cluster
4. **Create subdirectories** once a folder holds 8+ notes and 3+ of them share a group

## When to Create Subdirectories

Two numbers, applied in order:

1. **The folder reaches 8+ notes.** Below that, `ls` is easy to scan and the folder stays flat, however clearly its notes group.
2. **A group of 3+ notes gets a subfolder.** Signs of a group:
   - A common prefix in filenames (e.g., `play-functions-*.md`)
   - A shared topic that became obvious after writing

Notes that belong to no group of 3 stay at the folder root. A subfolder that itself reaches 8+ notes is split the same way.

**Don't create prematurely:**
❌ Empty folder structure before writing
✅ Write notes first, structure later

## Directory Patterns

**Flat is better than nested:**
```
naming/
├── variables-snake-case.md
├── components-pascalcase.md
└── boolean-prefix.md
```

**Subfolders only for clear groupings** (3+ notes, in a folder of 8+):
```
storybook/
├── documentation/       # 5 notes
├── play-functions/      # 6 notes
└── story-naming.md      # Standalone
```

## Benefits

**Adapts to reality**: Structure reflects actual content
**Reveals connections**: Writing notes reveals unexpected links
**Easy to reorganize**: Not rigid, easy to refactor
**No artificial constraints**: Don't force wrong categories

## Related Notes
- [Cross-Linking Notes](/principles/documentation/linking/cross-linking-notes.md)
- [Single Idea Per Note](/principles/documentation/structure/single-idea-per-note.md)
- [Folder README, Not Index](/principles/documentation/structure/folder-readme.md) - optional orientation once a folder has enough notes to need it
