---
description: Review changes, update CHANGELOG, determine version type (major/minor/patch), and update package.json version
---

# Release Version

You are tasked with preparing a new release by analyzing changes, updating the changelog, determining the appropriate semantic version bump, and updating package.json.

## Overview

This command follows semantic versioning (semver):
- **MAJOR** (x.0.0): Breaking changes - incompatible API changes, removed features, changed behavior that breaks existing usage
- **MINOR** (0.x.0): New features - backward-compatible functionality additions
- **PATCH** (0.0.x): Bug fixes - backward-compatible bug fixes, documentation updates, minor improvements

## Step 1: Review Current State

Read the current version and changes:

1. **Read package.json** to get current version
2. **Read CHANGELOG.md** to see unreleased changes
3. **Run git commands** to see all changes since last release:
   ```bash
   git diff HEAD~5..HEAD  # Review recent commits
   git log --oneline -10   # See commit history
   git status              # Check current status
   ```

## Step 2: Analyze Changes for Version Type

Review the **[Unreleased]** section in CHANGELOG.md and determine version bump:

### BREAKING (MAJOR bump)
Look for indicators:
- Section labeled "### Changed" with **BREAKING** prefix
- Removed public features or APIs
- Changed behavior that would break existing code
- Renamed/restructured directories that affect consumers
- Changed file naming conventions
- Changed variable naming conventions (e.g., camelCase → snake_case)
- Changed architecture patterns that require code updates

### Features (MINOR bump)
Look for indicators:
- Section labeled "### Added"
- New notes, patterns, or standards
- New directories or topic areas
- New features that don't break existing usage
- Enhanced functionality (backward-compatible)

### Fixes (PATCH bump)
Look for indicators:
- Section labeled "### Fixed"
- Typo corrections in documentation
- Updated examples without changing patterns
- Clarifications to existing notes
- Link fixes

## Step 3: Determine Version Bump

Based on analysis, choose the version bump:

**Priority order** (if multiple types present):
1. BREAKING changes → MAJOR bump
2. New features → MINOR bump
3. Only fixes/docs → PATCH bump

Present your analysis to the user:

```
## Version Analysis

**Current version**: X.Y.Z

**Changes detected**:
- [List key changes from CHANGELOG]

**Recommended version bump**: [MAJOR/MINOR/PATCH]

**New version**: X.Y.Z

**Reasoning**:
[Explain why this version bump was chosen based on the changes]
```

## Step 4: Get User Confirmation

Ask the user to confirm:

> I recommend bumping the version to **X.Y.Z** ([MAJOR/MINOR/PATCH]).
>
> This will:
> 1. Move [Unreleased] changes to a new version section in CHANGELOG.md
> 2. Update package.json version to X.Y.Z
> 3. Update the date in CHANGELOG.md to today's date
>
> Proceed with this release? (yes/no)

Wait for user confirmation before proceeding.

## Step 5: Update CHANGELOG.md

Once confirmed, update CHANGELOG.md:

1. **Replace** `## [Unreleased]` section with:
   ```markdown
   ## [Unreleased]

   ### Added

   ### Changed

   ### Fixed

   ### Removed

   ## [X.Y.Z] - YYYY-MM-DD
   ```

2. **Move all content** from old [Unreleased] section under the new `## [X.Y.Z] - YYYY-MM-DD` heading

3. **Use today's date** in YYYY-MM-DD format

Example transformation:
```markdown
## [Unreleased]

### Added
- New feature X
- New feature Y

### Changed
- Updated pattern Z

---

BECOMES:

---

## [Unreleased]

### Added

### Changed

### Fixed

### Removed

## [1.2.0] - 2025-11-18

### Added
- New feature X
- New feature Y

### Changed
- Updated pattern Z
```

## Step 6: Update package.json

Update the version field in package.json:

```json
{
  "version": "X.Y.Z"
}
```

## Step 7: Verify Changes

After updates:

1. **Read CHANGELOG.md** - Confirm changes moved correctly
2. **Read package.json** - Confirm version updated
3. **Run git diff** - Show user what changed

Present summary:

```
## Release Preparation Complete

**Version updated**: X.Y.Z

**Files modified**:
- CHANGELOG.md - Moved unreleased changes to version X.Y.Z
- package.json - Updated version field

**Next steps**:
You may want to:
1. Review the changes with `git diff`
2. Commit with `git commit -m "Release version X.Y.Z"`
3. Create a git tag with `git tag vX.Y.Z`
4. Push changes and tags with `git push && git push --tags`
```

## Important Notes

- **Be conservative**: When in doubt between version types, choose the more significant bump (MINOR over PATCH, MAJOR over MINOR)
- **Breaking changes always win**: Even one breaking change requires a MAJOR bump
- **Empty sections**: Leave empty sections (### Added, ### Changed, etc.) in the new [Unreleased] block
- **Date format**: Always use YYYY-MM-DD format (ISO 8601)
- **Don't auto-commit**: Let the user review and commit manually
- **Preserve formatting**: Keep the CHANGELOG.md format consistent with Keep a Changelog standard

## Edge Cases

### No Unreleased Changes
If [Unreleased] section is empty, inform the user:
> No unreleased changes found in CHANGELOG.md. Cannot prepare a release without changes.

### Version is already at X.Y.Z
If suggested version matches current version:
> The suggested version (X.Y.Z) is the same as the current version. Are you sure you want to proceed?

### Multiple BREAKING changes
Clearly list all breaking changes in your analysis so the user understands the scope.
