# Testing Pattern Matching

Pattern matching syntax for running tests on specific stories or components.

## Why Use Patterns?

**Benefits**:
- Test specific components during development
- 10x faster than running full suite
- Focus on what you're actively working on

**Performance**:
- Full suite: ~60 seconds (100 stories)
- Single component: ~5 seconds (5 stories)

## Basic Pattern Syntax

### Test by Component Name

```bash
npx test-storybook --pattern="Button"
```

Tests all files matching "Button":
- `Button.stories.js`
- `ButtonGroup.stories.js`
- `SubmitButton.stories.js`

### Test Specific File

```bash
npx test-storybook --pattern="FileDropzone.stories"
```

Tests only `FileDropzone.stories.js` (all stories in that file).

### Test by Directory

```bash
npx test-storybook --pattern="components/forms"
```

Tests all story files in `components/forms/` directory.

### Test Multiple Components

```bash
npx test-storybook --pattern="Button|Dialog|Modal"
```

Uses regex `|` (OR) to test multiple components.

## Regex Patterns

Full regex syntax supported:

### Starts With

```bash
# All components starting with "Export"
npx test-storybook --pattern="^Export"
```

Matches: `ExportDialog`, `ExportButton`, etc.

### Ends With

```bash
# All Dialog components
npx test-storybook --pattern="Dialog$"
```

Matches: `ConfirmDialog`, `AlertDialog`, etc.

### Multiple Exact Matches

```bash
npx test-storybook --pattern="(Button|Card|Table)"
```

Matches only: `Button`, `Card`, `Table`

### Path Matching

```bash
# Only stories in ui directory
npx test-storybook --pattern="ui/"

# Only stories in components/forms
npx test-storybook --pattern="components/forms/"
```

## Filtering Strategies

### By Feature Area

```bash
# Authentication components
npx test-storybook --pattern="src/components/auth"

# Navigation components
npx test-storybook --pattern="src/components/nav"

# Form components
npx test-storybook --pattern="src/components/forms"
```

### By Complexity

```bash
# Simple presentational components
npx test-storybook --pattern="src/components/ui"

# Complex container components
npx test-storybook --pattern="src/components/containers"
```

### Recent Changes

```bash
# Find recently modified story files
git diff --name-only main | grep stories

# Test those specific files
npx test-storybook --pattern="FileDropzone|ExportDialog"
```

**Automated**:
```bash
# Get changed story files and test them
changed_files=$(git diff --name-only main | grep stories | sed 's/.stories.js//' | xargs basename -a | paste -sd "|" -)
npx test-storybook --pattern="$changed_files"
```

## Case Sensitivity

Patterns are **case-sensitive**:

```bash
# These are different:
--pattern="button"     # Matches: button.stories.js
--pattern="Button"     # Matches: Button.stories.js

# Won't match each other!
```

**Tip**: Use exact component name case.

## Common Patterns

### One Component

```bash
npx test-storybook --pattern="ComponentName"
```

### All Forms

```bash
npx test-storybook --pattern="forms/"
```

### All Dialogs and Modals

```bash
npx test-storybook --pattern="Dialog|Modal"
```

### All UI Components

```bash
npx test-storybook --pattern="ui/"
```

## Limitations

### Cannot Test Single Story

Test-runner operates at the **file level** only.

To test a single story within a file:

**Option 1**: Use Storybook dev UI
```bash
npm run storybook:dev
# Navigate to specific story in browser
```

**Option 2**: Temporarily rename other files
```bash
mv Button.stories.js Button.ignore.stories.js
npx test-storybook
mv Button.ignore.stories.js Button.stories.js
```

**Option 3**: Comment out other stories (not recommended)

### No Metadata Filtering

Not supported:
- Testing by tag/label
- Testing by category
- Testing by story metadata

**Workaround**: Use consistent naming conventions.

## Combining Patterns

### Pattern + Watch Mode

```bash
npx test-storybook --pattern="Button" --watch
```

Re-runs tests when Button.stories.js changes.

### Pattern + Verbose

```bash
npx test-storybook --pattern="Dialog" --verbose
```

Shows detailed output for Dialog stories only.

### Pattern + Headed

```bash
npx test-storybook --pattern="Form" --headed
```

Opens visible browser for Form component tests.

## Related Notes

- [Testing Development Workflow](/storybook/testing/testing-development-workflow.md) - Daily testing and debugging patterns
