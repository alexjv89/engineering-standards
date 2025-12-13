# Brevity

Remove filler words, not structure. Minimum words for specificity.

## Principle

Notes are for **repeat reference**, not first-time learning. Remove unnecessary elaboration while preserving scannability.

**Remove**: Filler words, redundant phrases, obvious statements
**Keep**: Structure that aids comprehension (bullets, whitespace, code blocks) 

## Good Example

```markdown
Boolean variables use `is_`, `has_`, or `should_` prefixes.

const is_active = true;
const has_errors = false;
```

Information-dense. Everything needed, nothing extra.

## Bad Examples

### Over-explained (filler words)
"When writing code with boolean variables, it's important to follow naming conventions that make it clear that the variable contains a boolean value..."

Same info buried in filler. Wastes time on repeat reads.

### Over-compressed (lost structure)
```markdown
Use is_, has_, should_ prefixes, e.g. is_active, has_errors, should_validate.
```

Bullet points converted to prose. Harder to scan, not actually briefer.

**Better**:
```markdown
Boolean prefixes:
- `is_` - state (is_active)
- `has_` - possession (has_errors)
- `should_` - intent (should_validate)
```

Structure = scannable = brief for repeat reference.

## Why This Matters

Repeat reference speed:
- **Well-structured brief**: Pattern visible in 2 seconds
- **Over-compressed**: Must parse dense prose (10 seconds)
- **Over-explained**: Must scan paragraphs (30 seconds)

Bullets, whitespace, code blocks = visual pattern recognition = fast scanning.

## What to Remove

- "It's important to note that..."
- "When working with X, you should..."
- "This allows you to..."
- Explanations of things obvious from code examples
- Restating what the title already says

## What to Keep

- Bullet points (already optimized for scanning)
- Line breaks between concepts
- Code examples (visual > prose)
- Section headings (navigational anchors)

## Related Notes
- [Single Idea Per Note](/principles/documentation/single-idea-per-note.md)
- [Meaningful Titles](/principles/documentation/meaningful-titles.md)
- [Document Density](/principles/documentation/document-density.md) - What to include/exclude for LLMs
