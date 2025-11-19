# Test Runner Configuration

Core configuration settings for `@storybook/test-runner`.

## What is the Test Runner?

The test-runner is a Playwright-powered tool that:
1. Starts a headless browser (Chromium)
2. Visits each story's unique URL
3. Executes any play functions defined in the story
4. Captures console errors and warnings
5. Reports test results

**Purpose**: Automated regression testing by treating stories as test cases.

## Configuration File

**Location**: `.storybook/test-runner.js`

## Key Settings

```javascript
module.exports = {
  // Playwright browser configuration
  launch: {
    channel: 'chromium',
  },

  // Test execution settings
  maxWorkers: 2,  // Parallel test execution

  // Viewport for all stories
  viewport: { width: 1366, height: 768 },

  // Timing
  timeout: {
    action: 10000,      // 10s for user actions
    navigation: 30000,  // 30s for page loads
  },

  // Retry failed tests once
  retries: 1,
};
```

## Browser Settings

### Browser Type

```javascript
launch: {
  channel: 'chromium',
}
```

**Browser**: Chromium (Chrome/Edge)
**Mode**: Headless (no visible window)

**Why Chromium**: Most widely used browser, good Playwright support.

### Viewport

```javascript
viewport: { width: 1366, height: 768 }
```

**Fixed size**: Ensures consistent test results across machines.

**Default**: 1366x768 (common laptop resolution)

**Customize**: Change if testing mobile/tablet layouts.

## Performance Settings

### Parallel Workers

```javascript
maxWorkers: 2
```

**Workers**: Number of parallel test executions

**Default**: 2 workers
**More workers**: Faster tests (if you have CPU resources)
**Fewer workers**: More stability on low-resource machines

**CI optimization**:
```javascript
maxWorkers: process.env.CI ? 4 : 2
```

More workers on CI servers with more CPU.

## Reliability Settings

### Retries

```javascript
retries: 1
```

**Retries**: Automatic retry count on test failure

**Default**: 1 retry
**Benefit**: Handles flaky tests from timing issues
**Result**: Second failure = real test failure

**Disable retries**:
```javascript
retries: 0  // Fail immediately
```

## Timeout Settings

### Action Timeout

```javascript
timeout: {
  action: 10000,  // 10 seconds
}
```

**Action timeout**: Max time for user interactions (clicks, typing, etc.)

**Default**: 10 seconds
**Increase**: For slow interactions or complex components

**Example**:
```javascript
timeout: {
  action: 15000,  // 15 seconds for slow components
}
```

### Navigation Timeout

```javascript
timeout: {
  navigation: 30000,  // 30 seconds
}
```

**Navigation timeout**: Max time for page loads

**Default**: 30 seconds
**Increase**: For slow-loading components or network requests

**Example**:
```javascript
timeout: {
  navigation: 60000,  // 60 seconds for slow pages
}
```

### CI-Specific Timeouts

CI is often slower than local machines:

```javascript
timeout: {
  action: process.env.CI ? 15000 : 10000,
  navigation: process.env.CI ? 60000 : 30000,
}
```

## Reporting

### Test Results Directory

Generated in `test-results/` directory:
- HTML report with screenshots of failures
- JSON results for CI integration
- Console output during test run

### Viewing Reports

```bash
# After tests complete
npx playwright show-report test-results
```

Opens interactive HTML report in browser.

## Common Customizations

### More Workers for CI

```javascript
maxWorkers: process.env.CI ? 4 : 2
```

### Longer Timeouts for Slow Components

```javascript
timeout: {
  action: 15000,      // Increased from 10s
  navigation: 60000,  // Increased from 30s
}
```

### No Retries (Strict Mode)

```javascript
retries: 0  // Fail immediately, no retries
```

### Different Viewport

```javascript
viewport: { width: 375, height: 667 }  // Mobile size
```

## Dependencies

Required packages:

```json
{
  "@storybook/test-runner": "^0.19.0",
  "playwright": "^1.48.0"
}
```

## Related Notes

- [Test Runner Hooks](/storybook/testing/test-runner-hooks.md) - Pre/post visit hooks
- [Test Runner Filtering](/storybook/testing/test-runner-filtering.md) - Ignoring stories
- [Testing Development Workflow](/storybook/testing/testing-development-workflow.md) - Executing tests
