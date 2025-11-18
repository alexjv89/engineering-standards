# Update Changelog

Automatically analyze git changes and update CHANGELOG.md with appropriate entries in the [Unreleased] section.

## Overview

This command examines staged and unstaged changes, categorizes them, and updates the CHANGELOG.md file following the Keep a Changelog format.

## Step 1: Analyze Git Changes

Run git commands to understand what changed:

```bash
# Show staged changes
git diff --cached --name-status

# Show unstaged changes
git diff --name-status

# Show recent commits not yet in changelog
git log --oneline -5

# Get detailed stats
git diff HEAD --stat
```

## Step 2: Read Current State

Read CHANGELOG.md to understand:
- Current [Unreleased] section content
- What's already documented
- Format and structure to maintain

## Step 3: Categorize Changes

Analyze each changed file and categorize into:

### Added
- New files created (new `.md` notes, new directories)
- New features or patterns
- New notes in existing directories
- New slash commands

**Indicators:**
- `git status` shows "new file:"
- New `.md` files in topic directories
- New directories created

### Changed
- Modified existing files with content updates
- Updated patterns or examples
- Reorganized content
- Enhanced existing notes

**Indicators:**
- `git status` shows "modified:"
- Existing files with substantial changes
- Updated examples or patterns

### Fixed
- Typo corrections
- Link fixes
- Documentation clarifications
- Bug fixes in examples

**Indicators:**
- Minor changes to existing files
- Typo/grammar fixes
- Link corrections
- Small clarifications

### Removed
- Deleted files
- Removed sections
- Deprecated patterns

**Indicators:**
- `git status` shows "deleted:"
- Removed references

## Step 4: Generate Changelog Entries

For each category, generate appropriate entries:

### Format for Added entries:
```markdown
### Added
- **[Feature/Topic Area] ([N new notes])**:
  - `directory/` - Brief description
  - `file-name.md` - What it covers
  - `other-file.md` - What it covers
```

### Format for Changed entries:
```markdown
### Changed
- **[Topic/Area] Updates**:
  - `file-name.md` - What changed and why
  - Description of reorganization/improvements
```

### Format for Fixed entries:
```markdown
### Fixed
- Fixed typos in `file-name.md`
- Corrected links in `directory/index.md`
```

### Format for Removed entries:
```markdown
### Removed
- `file-name.md` - Reason for removal
```

## Step 5: Smart Categorization Rules

**Prioritize by impact:**

1. **New directory** → Added section with all files listed
2. **Multiple new files in same directory** → Group under directory heading
3. **Modified index.md** → Usually means Added (new items in directory)
4. **Modified existing note** → Changed (unless trivial fix)
5. **Renamed file** → Changed (note the rename)

**Grouping rules:**
- Group related changes together (e.g., all testing/ changes)
- Use hierarchical bullets for clarity
- Keep entries concise but descriptive

## Step 6: Update CHANGELOG.md

Update the [Unreleased] section:

1. **Read current [Unreleased] content** - Don't overwrite existing entries
2. **Merge new entries** - Add to appropriate sections
3. **Maintain formatting** - Keep Keep a Changelog format
4. **Sort logically** - Most significant changes first within each category

**Merge strategy:**
- If section is empty, add all new entries
- If section has content, append new entries
- Remove empty placeholder sections if they remain empty
- Keep section order: Added, Changed, Fixed, Removed

## Step 7: Present Changes

Show the user what was added:

```
## Changelog Updated

**Added to [Unreleased] section:**

### Added
- [List new entries]

### Changed
- [List new entries]

### Fixed
- [List new entries]

**Summary:**
- X new files documented
- Y modified files documented
- Z total changes categorized

**Next steps:**
1. Review CHANGELOG.md to verify entries
2. Make any manual adjustments if needed
3. Commit with: git commit -m "docs: [description of changes]"
```

## Intelligence Guidelines

### Be Smart About Context

**Testing directory changes:**
- New test notes → "Testing Documentation Expansion"
- Multiple related notes → Group under topic heading
- Updated index.md → Usually indicates Added, not Changed

**Architecture directory changes:**
- New pattern → "Added [Pattern Name] pattern"
- Database notes → "Database Patterns"
- Component patterns → "Component Architecture"

**Naming directory changes:**
- New convention → "Added [Convention] naming standard"

**Examples directory changes:**
- New example → "Added [Example Name] example demonstrating [concept]"

### Infer Intent from File Content

If possible, read the changed files to understand:
- What pattern/concept they document
- Whether it's a new addition or update
- The significance of the change

### Avoid Duplication

Check if similar changes are already documented in [Unreleased]:
- Don't add duplicate entries
- Merge related entries if possible
- Update existing entries rather than adding new ones

## Edge Cases

### No Changes Detected
If git shows no changes:
```
No changes detected. CHANGELOG.md is up to date.
```

### Only Trivial Changes
If only minor typos/formatting:
```
Only trivial changes detected (typos, formatting).

These may not warrant changelog entries. Do you want to add them anyway?
```

### Large Changesets
If 20+ files changed:
```
Large changeset detected (X files). Grouping related changes...
```

Group by directory and summarize at higher level.

### CHANGELOG.md itself changed
Ignore changes to CHANGELOG.md when analyzing - don't create recursive entries!

## Example Transformation

**Git shows:**
```
A  testing/jest-mocking-patterns.md
A  testing/semantic-mocking.md
A  testing/mock-cleanup.md
M  testing/index.md
M  testing/setup-teardown.md
```

**Generated entry:**
```markdown
### Added
- **Jest Mocking Patterns (3 new notes)**:
  - `jest-mocking-patterns.md` - Module vs function mocking patterns
  - `semantic-mocking.md` - Implementation-based mocking
  - `mock-cleanup.md` - jest.clearAllMocks() pattern

### Changed
- `testing/index.md` - Added new mocking pattern notes
- `setup-teardown.md` - Added jest.clearAllMocks() examples
```

## Important Notes

- **Don't auto-commit** - Let user review and commit
- **Preserve existing [Unreleased] entries** - Only add new ones
- **Follow Keep a Changelog format** strictly
- **Be descriptive but concise** - Each entry should be clear
- **Group related changes** - Better readability
- **Infer meaning from context** - Use directory structure and file names

## What NOT to Include

- Changes to `.claude/` directory (internal tooling)
- Changes to `.gitignore`, `package-lock.json` (unless significant)
- Temporary files or build artifacts
- The CHANGELOG.md file itself
