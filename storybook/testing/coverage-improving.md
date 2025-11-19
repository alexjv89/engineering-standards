# Improving Coverage

Strategies and workflows for increasing Storybook coverage to 100%.

## Basic Workflow

### 1. Identify Missing Components

```bash
npm run storybook:coverage
```

Review console output for ❌ missing stories.

### 2. Create Story File

```bash
# For component at src/components/ui/Button.jsx
touch src/components/ui/Button.stories.jsx
```

**Important**: Story must be in **same directory** as component.

### 3. Write Stories

Follow [User Journey Pattern](/storybook/storybook-user-journey-pattern.md):

```javascript
import Button from './Button';

const meta = {
  title: 'UI/Button',
  component: Button,
};

export default meta;

export const AfterUserClicksAction = {
  args: {
    children: 'Click me',
    variant: 'primary',
  },
};
```

### 4. Verify Coverage Improved

```bash
npm run storybook:coverage
```

Should show Button.jsx as ✅ covered.

## Prioritization Strategies

### Strategy 1: Focus on 0% Directories

Target directories with no coverage first (quick wins):

```
src/components/nav: 0/10 (0%) ← Start here
```

**Why**: Creates momentum, easy to measure progress.

### Strategy 2: Finish Almost-Complete Directories

Target directories at 80%+ first:

```
src/components/forms: 12/15 (80%) ← Finish these 3
```

**Why**: Achieves 100% in specific areas, provides sense of completion.

### Strategy 3: Core Components First

Focus on most-used components regardless of directory:

- Buttons
- Forms (Input, Select, Textarea)
- Cards
- Dialogs
- Tables

**Why**: Maximum impact - these components used across app.

## Handling Non-Components

### Utilities Flagged as Needing Stories

**Problem**: Coverage script flags `helpers.jsx` as missing story.

**Diagnosis**: Check if file actually contains components or utilities.

```javascript
// src/utils/helpers.jsx
export function calculate_total(items) {  // Not a component!
  return items.reduce((sum, item) => sum + item.price, 0);
}
```

**Solution 1** - Rename to `.js`:
```bash
mv src/utils/helpers.jsx src/utils/helpers.js
```

**Solution 2** - Add to exclusions (not recommended):
```javascript
// In scripts/storybook-coverage.js
const exclude_patterns = [
  /\.test\./,
  /\.stories\./,
  /helpers\.jsx$/,
];
```

**Best practice**: Use `.jsx` only for components that return JSX.

### Hooks Flagged as Needing Stories

**Problem**: Custom hooks flagged as missing stories.

**Fix**: Rename to `.js`:
```bash
mv src/hooks/use_auth.jsx src/hooks/use_auth.js
```

**Alternative**: Document hooks separately (not in Storybook).

## Bulk Coverage Improvement

### Generate Story Stubs

Create basic story files for all missing components:

```bash
# Find all components without stories
npm run storybook:coverage | grep "❌" | cut -d' ' -f2 > missing.txt

# Create story file for each
while read component; do
  story_file="${component%.*}.stories.jsx"
  touch "$story_file"
  echo "Created: $story_file"
done < missing.txt
```

**Result**: Coverage jumps to 100%, but stories are empty.

**Next step**: Fill in actual stories following user journey pattern.

## Coverage Maintenance

### Pre-Commit Check

Add to git pre-commit hook:

```bash
#!/bin/bash
# .git/hooks/pre-commit

npm run storybook:coverage
exit_code=$?

if [ $exit_code -ne 0 ]; then
  echo "❌ Coverage below 100%. Create stories before committing."
  exit 1
fi

exit 0
```

**Effect**: Prevents commits that reduce coverage.

### Pull Request Workflow

**Before creating PR**:
```bash
# 1. Run tests
npm run storybook:test:ci

# 2. Check coverage
npm run storybook:coverage

# 3. If coverage < 100%, create missing stories
# 4. Commit coverage report
git add dev_docs/storybook/
git commit -m "Update coverage report"
```

**In PR description**: Include coverage change:
```markdown
## Coverage
Before: 26.3% (44/167)
After: 28.1% (47/167)
Added stories for: Button, Card, Input
```

## Coverage vs. Quality

**Remember**: 100% coverage ≠ good stories

### Coverage Ensures:
- Every component is documented
- Every component has at least one story
- No components are forgotten

### Quality Requires:
- Following [User Journey Pattern](/storybook/storybook-user-journey-pattern.md)
- Writing meaningful [Descriptions](/storybook/documentation/custom-description-banner.md)
- Adding appropriate [Play Functions](/storybook/play-functions/basics.md)
- Covering edge cases and error states

**Example of low-quality but covered**:
```javascript
// Button.stories.jsx
export default { title: 'Button', component: Button };
export const Default = { args: {} };  // Minimal, not user-focused
```

**Example of high-quality**:
```javascript
// Button.stories.jsx
/**
 * USER JOURNEY: After user clicks primary action
 * Tests: Rendering, click interaction, disabled state
 */
export const AfterUserClicksPrimaryAction = {
  args: {
    variant: 'primary',
    children: 'Save Changes',
  },
  parameters: {
    description: 'Primary button for main user actions like saving or submitting',
  },
};
```

## Troubleshooting

### Coverage Dropped After Refactoring

**Diagnosis**:
```bash
git diff main -- 'src/**/*.stories.*'
```

**Common causes**:
1. Renamed component without updating story
2. Moved component to different directory
3. Deleted story file accidentally

**Fix**: Update or recreate matching story file.

### Story Exists But Not Counted

**Check**:
1. Same directory as component?
2. Exact name match (case-sensitive)?
3. Correct extension (`.stories.jsx` or `.stories.js`)?

**Debug**:
```bash
# List all story files
find src -name "*.stories.*"

# Check specific component
ls -la src/components/ui/Button*
```

## Related Notes

- [Coverage Script](/storybook/testing/coverage-script.md) - How coverage is calculated
- [Coverage Requirements](/storybook/testing/coverage-requirements.md) - What counts as covered
- [Coverage Reports](/storybook/testing/coverage-reports.md) - Understanding output
- [User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - Writing quality stories
