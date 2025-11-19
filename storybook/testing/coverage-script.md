# Coverage Script

How the Storybook coverage script works to track which components have stories.

## Location

**Script**: `scripts/storybook-coverage.js`

**Run it**:
```bash
npm run storybook:coverage
```

## What It Does

The script performs 5 steps:

### 1. Scans Files

Finds all `.jsx` and `.tsx` files in `/src` directory recursively.

### 2. Excludes Non-Components

Filters out:
- Test files (`*.test.jsx`, `*.spec.jsx`)
- Story files (`*.stories.jsx`)
- Next.js special files (`page.jsx`, `layout.jsx`, `loading.jsx`, `error.jsx`, `not-found.jsx`)

**Exclusion patterns**:
```javascript
const exclude_patterns = [
  /\.test\./,
  /\.stories\./,
  /page\.jsx$/,
  /layout\.jsx$/,
  /loading\.jsx$/,
  /error\.jsx$/,
  /not-found\.jsx$/,
];
```

### 3. Matches with Stories

For each component file, searches for matching `.stories.*` file in the same directory.

**Matching logic**:
- `Button.jsx` → looks for `Button.stories.jsx` or `Button.stories.js`
- `components/Card.jsx` → looks for `components/Card.stories.jsx`

**Same directory requirement**: Story must be in exact same directory as component.

### 4. Generates Reports

Creates three outputs:
- Console report with colored summary
- Markdown report (`dev_docs/storybook/coverage-report.md`)
- SVG badge (`dev_docs/storybook/coverage-badge.svg`)

See [Coverage Reports](/storybook/testing/coverage-reports.md) for details.

### 5. Exits with Error Code

**Exit code 0**: Coverage = 100%
**Exit code 1**: Coverage < 100%

**Use case**: CI pipelines use exit code to fail builds if coverage requirement not met.

## Console Output

Example output:

```
=== Storybook Coverage Report ===
✅ Components with stories: 44
❌ Components without stories: 123
📊 Coverage: 26.3%

Directory Breakdown:
/src/components/ui: 5/20 (25%)
/src/components/forms: 8/15 (53%)
/src/components/nav: 0/10 (0%)

Missing Stories:
❌ src/components/ui/Button.jsx
❌ src/components/ui/Card.jsx
...
```

**Color coding**:
- Green ✅ for components with stories
- Red ❌ for missing stories
- Percentages color-coded (red < 50%, yellow 50-80%, green > 80%)

## Customizing Exclusions

Edit the script to exclude specific files:

```javascript
// In scripts/storybook-coverage.js
const exclude_patterns = [
  /\.test\./,
  /\.stories\./,
  /page\.jsx$/,
  /layout\.jsx$/,
  /loading\.jsx$/,
  /error\.jsx$/,
  /not-found\.jsx$/,
  /helpers\.jsx$/,  // Add custom exclusion
];
```

**When to add exclusions**:
- Utilities that shouldn't use `.jsx` extension
- Infrastructure components that don't need stories
- Generated files

**Better approach**: Rename non-components to `.js` instead of excluding.

## Coverage Calculation

```
coverage_percentage = (components_with_stories / total_components) * 100
```

**Example**:
- Total components: 167
- With stories: 44
- Coverage: 44 / 167 * 100 = 26.3%

## Related Notes

- [Coverage Requirements](/storybook/testing/coverage-requirements.md) - What counts as component/covered
- [Coverage Reports](/storybook/testing/coverage-reports.md) - Report formats and badges
