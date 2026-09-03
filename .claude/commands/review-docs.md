---
description: Review documentation quality against all principles
---

# Review Documentation Quality

Review all markdown files against documentation principles: brevity, MECE, single idea per note, meaningful titles, cross-linking notes, and emergent structure.

## Reference Documents

Read documentation principles:
- `principles/documentation/structure/single-idea-per-note.md`
- `principles/documentation/voice/meaningful-titles.md`
- `principles/documentation/voice/brevity.md`
- `principles/documentation/structure/mece.md`
- `principles/documentation/linking/cross-linking-notes.md`
- `principles/documentation/structure/emergent-structure.md`

## Step 1: Inventory Files

Find all markdown files in:
- `naming/*.md`
- `testing/*.md`
- `architecture/*.md`
- `git-workflow/*.md`
- `storybook/*.md`
- `principles/*.md`

**Exclude**:
- Root-level: `README.md`, `CLAUDE.md`, `CHANGELOG.md`
- `templates/`
- `examples/`

## Step 2: Evaluate Each File

### Quality Checklist

**Single Idea Per Note**:
- [ ] Focuses on ONE specific concept/pattern/practice
- [ ] No multiple unrelated concepts bundled together

**Meaningful Titles**:
- [ ] Filename clearly describes the single concept
- [ ] Descriptive enough to determine relevance without reading

**Brevity**:
- [ ] 20-60 lines typical (flag if significantly over)
- [ ] No unnecessary elaboration or fluff
- [ ] Dense, information-rich for repeat reference
- [ ] Pattern visible in first few lines

**MECE (Mutually Exclusive, Collectively Exhaustive)**:
- [ ] No overlap with other notes in same directory
- [ ] Clear boundaries between related concepts
- [ ] Together with related notes, covers topic completely

**Cross-Linking Notes**:
- [ ] Has 2-5 related note links
- [ ] Links placed at end of note
- [ ] Uses relative paths

**Code Examples** (where applicable):
- [ ] Includes practical code examples
- [ ] Examples are clear and focused

### Scoring

Rate each file:
- ✅ **Good**: Meets all applicable criteria
- ⚠️ **Needs Improvement**: 1-2 violations
- ❌ **Major Issues**: Multiple violations

## Step 3: Report

Generate report:

### Summary Statistics
- Total files reviewed: X
- Good (✅): X
- Needs improvement (⚠️): X
- Major issues (❌): X

### Detailed Findings

For each problem file:
- **File**: `path/to/file.md`
- **Status**: ⚠️ or ❌
- **Issues**:
  - [ ] **Brevity**: Too verbose (X lines, should be 20-60)
  - [ ] **Single Idea**: Covers multiple concepts (list them)
  - [ ] **MECE**: Overlaps with `other-file.md`
  - [ ] **Links**: Missing related links / too many links
  - [ ] **Title**: Not descriptive enough
  - [ ] **Examples**: Missing code examples
- **Recommendation**: Specific action (condense, split, add links, etc.)

## Step 4: Improvement Plan

### Files to Condense (Too Verbose)
List files exceeding 60 lines with unnecessary content.

For each:
- File: `path/to/file.md`
- Current length: X lines
- Target: ~40 lines
- What to remove: Fluff, redundant examples, obvious explanations

### Files to Split (Multiple Concepts)
List files covering multiple concepts.

For each:
- Current file: `path/to/file.md`
- Proposed split:
  - `concept-1.md` - Description
  - `concept-2.md` - Description

### Files to Fix MECE Violations
List files with overlap or gaps.

For each:
- Files with overlap: `file1.md`, `file2.md`
- Issue: What content overlaps
- Fix: Move X to file1, Y to file2

### Files Missing Links/Examples
List files missing required elements.

For each:
- File: `path/to/file.md`
- Add: Links to related notes / Code examples / Both

## Step 5: Get Approval

Present plan:

> I've reviewed all markdown files against documentation principles. Found:
> - X files to condense (too verbose)
> - X files to split (multiple concepts)
> - X files with MECE violations
> - X files missing links/examples
>
> Would you like me to proceed? You can also:
> - Review details for specific files
> - Skip certain changes
> - Modify approach

Wait for confirmation.

## Step 6: Execute Changes

Once approved:

1. **Condense**: Remove fluff, keep pattern clear
2. **Split**: Create new files, update links
3. **Fix MECE**: Move content to correct files
4. **Add elements**: Add missing links/examples

Mark each complete as you go.

## Important Notes

- Use `principles/documentation/` as source of truth
- Brevity is critical - most violations are excessive verbosity
- When in doubt, condense and split over merge
- Preserve all useful content, just make it denser
- Update cross-references when moving/splitting
