# Coverage Reports

Understanding coverage report formats and using coverage badges.

## Report Formats

The coverage script generates three outputs:

### 1. Console Report

**Output**: Terminal (stdout)

**Format**:
```
=== Storybook Coverage Report ===
✅ Components with stories: 44
❌ Components without stories: 123
📊 Coverage: 26.3%

Directory Breakdown:
/src/components/ui: 5/20 (25%) ← Low coverage
/src/components/forms: 12/15 (80%) ← Almost done
/src/components/nav: 0/10 (0%) ← Not started

High Priority Missing:
❌ src/components/ui/Button.jsx
❌ src/components/ui/Card.jsx
❌ src/components/forms/Input.jsx
```

**Use case**: Quick check during development.

**Color coding**:
- ✅ Green for covered components
- ❌ Red for missing stories
- Coverage % color: red < 50%, yellow 50-79%, green ≥ 80%

### 2. Markdown Report

**Location**: `dev_docs/storybook/coverage-report.md`

**Format**:
```markdown
# Storybook Coverage Report
Generated: 2025-01-15

## Summary
- Total Components: 167
- With Stories: 44 (26.3%)
- Missing Stories: 123 (73.7%)

## Missing Stories by Directory

### src/components/ui (15 missing)
- ❌ Accordion.jsx
- ❌ Alert.jsx
- ❌ Avatar.jsx

### src/components/forms (3 missing)
- ❌ Input.jsx
- ❌ Select.jsx
- ❌ Textarea.jsx
```

**Use case**:
- Track progress over time (commit report with code)
- Share with team
- Reference in PR descriptions
- Historical tracking via git history

### 3. SVG Badge

**Location**: `dev_docs/storybook/coverage-badge.svg`

**Format**: SVG image with coverage percentage

**Embed in README**:
```markdown
![Storybook Coverage](dev_docs/storybook/coverage-badge.svg)
```

**Colors**:
- **Red** (< 50%): `#e74c3c`
- **Yellow** (50-79%): `#f39c12`
- **Green** (80-99%): `#2ecc71`
- **Bright Green** (100%): `#27ae60`

**Updates**: Regenerated on every `npm run storybook:coverage` run.

## Interpreting Results

### Coverage by Directory

Focus on directories with low coverage:

```
src/components/ui: 5/20 (25%) ← START HERE
src/components/forms: 12/15 (80%) ← Almost done
src/components/nav: 0/10 (0%) ← Not started
```

**Strategy**:
1. Start with 0% directories (quick wins)
2. Finish high-priority 80%+ directories (get to 100%)
3. Tackle large low-coverage directories

### High Priority Missing

Components that should have stories immediately:

```
❌ src/components/ui/Button.jsx
❌ src/components/ui/Card.jsx
❌ src/components/forms/Input.jsx
```

**Criteria for high priority**:
- Core reusable UI components
- Frequently used across app
- Complex components with edge cases

### Low Priority Missing

Components that might not need stories:

```
❌ src/app/page.jsx (Next.js page - should be excluded)
❌ src/utils/helpers.jsx (utility - should be .js)
```

**Action**: Verify if these should be:
- Excluded in coverage script
- Renamed to `.js` if not components
- Actually covered (create stories)

## Tracking Progress

### Commit Reports with Code

```bash
# Generate coverage
npm run storybook:coverage

# Commit report alongside code changes
git add dev_docs/storybook/coverage-report.md
git add dev_docs/storybook/coverage-badge.svg
git commit -m "Add Button stories (coverage: 26.3% → 28.1%)"
```

**Benefit**: Git history shows coverage trends.

### View Historical Trends

```bash
# See coverage changes over time
git log --oneline --all --grep="coverage" -- dev_docs/storybook/

# Compare coverage between branches
git diff main..feature-branch -- dev_docs/storybook/coverage-report.md
```

### Badge in README

Add to project README:

```markdown
# My Project

[![Storybook Coverage](dev_docs/storybook/coverage-badge.svg)](dev_docs/storybook/coverage-report.md)

Current Storybook coverage: 26.3%
Goal: 100%
```

**Benefit**: Visible progress tracker.

## Common Issues

### "Component has story but shows as uncovered"

**Possible causes**:
1. **Different directory**
   - Component: `src/components/Button.jsx`
   - Story: `src/stories/Button.stories.jsx`
   - **Fix**: Move story to `src/components/Button.stories.jsx`

2. **Name mismatch**
   - Component: `Button.jsx`
   - Story: `button.stories.jsx` (lowercase)
   - **Fix**: Rename to `Button.stories.jsx` (exact match)

3. **Wrong extension**
   - Story: `Button.story.jsx` (should be `.stories.jsx`)
   - **Fix**: Rename to `Button.stories.jsx`

### "Report shows 0% after creating stories"

**Cause**: Script caches results or hasn't run yet.

**Fix**: Re-run coverage script:
```bash
npm run storybook:coverage
```

## Related Notes

- [Coverage Script](/storybook/testing/coverage-script.md) - How reports are generated
- [Coverage Requirements](/storybook/testing/coverage-requirements.md) - What counts as covered
