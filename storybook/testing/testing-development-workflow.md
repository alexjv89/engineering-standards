# Testing Development Workflow

Daily workflows for testing stories during active development.

## Iterative Development

When actively working on a component:

### Setup

```bash
# Terminal 1: Keep Storybook running
npm run storybook:dev

# Terminal 2: Watch mode for specific component
npx test-storybook --watch --pattern="FileDropzone"
```

**Result**: Tests automatically re-run when `FileDropzone.stories.js` changes.

**Benefits**:
- Instant feedback on story changes
- No need to manually re-run tests
- Fast iteration cycle

### Development Loop

1. Edit component (`FileDropzone.jsx`)
2. Edit stories (`FileDropzone.stories.jsx`)
3. Watch terminal - tests auto-run
4. Fix issues, repeat

**Time savings**: 10x faster than running full suite each time.

## Before Committing

Ensure your changes don't break other components:

### Step 1: Test Modified Components

```bash
# Test only components you changed
npx test-storybook --pattern="Button|Card"
```

**Quick validation** of your specific changes.

### Step 2: Run Full Suite

```bash
# If step 1 passes, run everything
npm run storybook:test:ci
```

**Ensures** no unintended side effects.

### Step 3: Check Coverage

```bash
npm run storybook:coverage
```

**Verifies** coverage hasn't dropped.

## Writing New Stories

Workflow for creating stories for a new component:

### 1. Create Story File

```bash
# Component exists at: src/components/ui/Button.jsx
touch src/components/ui/Button.stories.jsx
```

### 2. Write First Story

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

### 3. Test in Watch Mode

```bash
# Terminal 1: Storybook dev
npm run storybook:dev

# Terminal 2: Watch tests for Button
npx test-storybook --watch --pattern="Button"
```

### 4. Add More Stories

Add stories for different user scenarios, watch tests pass.

### 5. Verify Coverage

```bash
npm run storybook:coverage
```

**Confirms** Button.jsx now shows as ✅ covered.

## Debugging Failed Stories

When a story test fails:

### 1. Identify Failure

```bash
npx test-storybook --pattern="FailingComponent"
```

### 2. Run in Verbose Mode

```bash
npx test-storybook --pattern="FailingComponent" --verbose
```

See detailed logs.

### 3. Run in Headed Mode

```bash
npx test-storybook --pattern="FailingComponent" --headed
```

Watch browser execute test.

### 4. Fix and Re-test

Edit story, watch tests auto-run (if in watch mode).

## Working on Multiple Components

Testing several components you're modifying:

```bash
# Watch multiple components
npx test-storybook --watch --pattern="Button|Card|Form"
```

All three components' tests re-run on changes to any of their stories.

## Combining with Coverage Checks

After implementing stories:

```bash
# 1. Test specific component
npx test-storybook --pattern="Button"

# 2. Verify coverage improved
npm run storybook:coverage
```

**Ensures** component is properly tracked as covered.

## Performance Tips

### Use Patterns to Avoid Full Suite

**Slow**:
```bash
# Runs all 100 stories (~60s)
npm run storybook:test:ci
```

**Fast**:
```bash
# Runs 5 stories (~5s)
npx test-storybook --pattern="Button"
```

**During development**: Always use patterns for speed.

**Before committing**: Run full suite to ensure no regressions.

### Keep Storybook Dev Running

**Don't**:
```bash
# Starting dev server takes time
npm run storybook:dev
npx test-storybook --pattern="Button"
# ... kill dev server ...
# ... restart for next test ...
```

**Do**:
```bash
# Terminal 1: Leave running all day
npm run storybook:dev

# Terminal 2: Run tests as needed
npx test-storybook --pattern="Button"
npx test-storybook --pattern="Card"
# ... dev server stays up ...
```

**Time savings**: ~10 seconds per test run (no server startup).

## Quick Reference

### Daily Development

```bash
# Terminal 1
npm run storybook:dev

# Terminal 2
npx test-storybook --watch --pattern="ComponentName"
```

### Before Commit

```bash
# 1. Modified components only
npx test-storybook --pattern="Button|Card"

# 2. Full suite
npm run storybook:test:ci

# 3. Coverage check
npm run storybook:coverage
```

### Debugging

```bash
# Verbose logs
npx test-storybook --pattern="Component" --verbose

# Show browser
npx test-storybook --pattern="Component" --headed
```

## Related Notes

- [Testing Pattern Matching](/storybook/testing/testing-pattern-matching.md) - Pattern syntax and filtering
- [Test Runner Config](/storybook/testing/test-runner-config.md) - Configuration settings
- [Coverage Reports](/storybook/testing/coverage-reports.md) - Understanding coverage output
