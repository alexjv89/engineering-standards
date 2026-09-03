# Meaningful Titles

Clear, descriptive titles that reflect the single idea inside.

## Purpose

Title should be specific enough to determine relevance **without reading content**.

## Format

```
topic/specific-concept.md
```

`topic/` = broad category (naming, testing, architecture)
`specific-concept` = exact pattern/practice being documented

## Naming Notes About a Coded Identifier

Some notes document a specific technical identifier — a field code, a config key, an API status code — rather than a general concept. Here the plain-language name alone is often ambiguous out of context (e.g. "item category" doesn't say category-of-what), and the raw code alone is opaque to anyone who doesn't already have it memorized.

Use a three-part filename instead of the plain `specific-concept.md` format:

```
facet-code-description.md
```

- `facet` = what class of thing this is (`field`, `table`, `endpoint`, `error-code`) — lets you scan a flat folder by note type (`ls field-*`) instead of opening each file
- `code` = the raw technical identifier, so the note is findable by someone arriving from the code itself (a query result, a config file, a stack trace)
- `description` = the plain-language name, resolving the ambiguity the code alone can't

✅ `field-pstyp-item-category.md` — not `item-category-pstyp.md` (facet buried mid-name, doesn't sort/scan with sibling field notes) or `field-pstyp.md` (drops the human-readable name, opaque to anyone without the code memorized)
✅ `field-knttp-account-assignment-category.md`
✅ `table-ekko-ekpo-header-and-line-items.md` — same folder, different facet: this note is about a table relationship, not a single field, so it doesn't get the `field-` prefix

This pattern only applies when the note is anchored to one coded identifier. Most notes — design decisions, process writeups, comparisons — have no code to append; don't force a `facet-` prefix onto them just for consistency.

## Good Examples

✅ `testing/aaa-pattern.md` - Arrange-Act-Assert test structure
✅ `architecture/container-presentational-pattern.md` - Separating logic from UI
✅ `git-workflow/branch-naming.md` - Branch naming conventions
✅ `naming/variables-snake-case.md` - Variable naming with snake_case

Each title precisely describes the single concept inside.

## Bad Examples

❌ `code-style.md` - What aspect of code style?
❌ `components.md` - Which component pattern?
❌ `testing.md` - What about testing?
❌ `react-patterns.md` - Covers multiple patterns

Can't determine relevance without reading content.

## Filename Conventions

✅ `container-presentational-pattern.md` (hyphens, lowercase)
❌ `container_presentational_pattern.md` (underscores)
❌ `ContainerPresentationalPattern.md` (PascalCase)

✅ `auto-open-pattern.md` (descriptive)
❌ `ao-pattern.md` (cryptic)

## Testing Your Title

**Can someone find this by searching the title?** → Add keywords if no
**Does the title describe the content accurately?** → Rename if no
**Is it specific enough to determine relevance?** → Be more specific if no

## Related Notes
- [Single Idea Per Note](/principles/documentation/structure/single-idea-per-note.md)
- [Brevity](/principles/documentation/voice/brevity.md)
