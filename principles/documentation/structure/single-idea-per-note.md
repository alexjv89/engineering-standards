# Single Idea Per Note

Each note addresses one specific concept, pattern, or practice.

## Principle

Create focused notes consumed independently, not comprehensive documents covering multiple topics.

**Why**: LLM context windows are precious. Load only relevant atomic notes, not entire documents.

## Examples

**Bad (Too Broad)**:
❌ `code-style.md` covering naming, imports, formatting, comments

**Good (Atomic)**:
✅ `naming/variables-snake-case.md` - Just variable naming
✅ `imports/order.md` - Just import ordering
✅ `formatting/indentation.md` - Just indentation rules

## Testing Atomicity

**Does this note explain ONE concept?**
- Yes → Atomic
- No → Split it

**Can someone understand this without reading other notes?**
- Yes → Good
- No → Add context or links

**Would splitting this make two self-contained notes?**
- Yes → Split it
- No → Keep together

## When to Combine

Only combine when topics are inseparable:

`testing/aaa-pattern.md` - Arrange-Act-Assert are three parts of ONE pattern

## When to Split

Split when topics can stand alone:

❌ `forms.md` covering validation, submission, styling

✅ Split into:
- `forms/validation.md`
- `forms/submission.md`
- `forms/styling.md`

## Related Notes
- [Meaningful Titles](/principles/documentation/voice/meaningful-titles.md)
- [Brevity](/principles/documentation/voice/brevity.md)
- [Cross-Linking Notes](/principles/documentation/linking/cross-linking-notes.md)
