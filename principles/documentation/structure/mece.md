# MECE (Mutually Exclusive, Collectively Exhaustive)

Organize so items don't overlap and nothing is missing.

## Definition

**Mutually Exclusive**: No overlap between items
**Collectively Exhaustive**: All possibilities covered

## Why It Matters

When splitting notes or organizing:
- Each note covers distinct territory (no duplication)
- All aspects of topic addressed (no gaps)

## Good MECE Example

**Naming conventions:**
- `variables-snake-case.md` - Variables only
- `components-pascalcase.md` - Components only
- `constants-screaming-snake.md` - Constants only
- `files-kebab-case.md` - Files only

**Mutually Exclusive**: No overlap
**Collectively Exhaustive**: All naming types covered

## Bad (Not MECE)

**Overlapping categories:**
- `frontend-naming.md` - Variables, components, files
- `react-naming.md` - Components, hooks ← overlaps with frontend
- `variable-naming.md` - Variables ← overlaps with frontend

**Missing coverage:**
- `variables-snake-case.md`
- `components-pascalcase.md`
Missing: constants, files, functions, etc.

## Applying MECE

**Split large note:**
❌ `testing.md` - Unit, integration, e2e, mocking, coverage

✅ Split into:
- `unit-tests.md`
- `integration-tests.md`
- `e2e-tests.md`
- `mocking.md` (applies across test types)
- `coverage.md` (applies across test types)

## Checking MECE

**Can same info be found in multiple notes?** → Not mutually exclusive, consolidate
**Are aspects of topic not covered anywhere?** → Not collectively exhaustive, add notes
**Do boundaries feel arbitrary?** → Rethink categorization

## Related Notes
- [Single Idea Per Note](/principles/documentation/structure/single-idea-per-note.md)
- [Emergent Structure](/principles/documentation/structure/emergent-structure.md)
