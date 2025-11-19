# Test Runner Hooks

Pre-visit and post-visit hooks for custom test logic.

## What are Hooks?

Hooks are functions that run before/after each story test:
- **Pre-visit hook**: Runs before story loads
- **Post-visit hook**: Runs after story renders and play functions complete

**Use case**: Custom setup, validation, logging, screenshots.

## Pre-Visit Hook

Runs **before** each story loads.

### Basic Example

```javascript
// .storybook/test-runner.js
module.exports = {
  async preVisit(page, context) {
    console.log(`Testing: ${context.title}`);
    await page.setViewportSize({ width: 1366, height: 768 });
  },
};
```

**Parameters**:
- `page`: Playwright page object
- `context`: Story context (id, title, name, etc.)

### Use Cases

#### 1. Logging Story Being Tested

```javascript
async preVisit(page, context) {
  console.log(`Testing: ${context.title}`);
}
```

#### 2. Setting Viewport Size

```javascript
async preVisit(page, context) {
  await page.setViewportSize({ width: 1366, height: 768 });
}
```

#### 3. Clearing Browser Storage

```javascript
async preVisit(page, context) {
  await page.evaluate(() => {
    localStorage.clear();
    sessionStorage.clear();
  });
}
```

#### 4. Mocking Global APIs

```javascript
async preVisit(page, context) {
  await page.evaluate(() => {
    window.fetch = async () => ({ ok: true, json: async () => ({}) });
  });
}
```

## Post-Visit Hook

Runs **after** story renders and play functions complete.

### Basic Example

```javascript
module.exports = {
  async postVisit(page, context) {
    // Check for console errors
    const errors = await page.evaluate(() => window.__errors);
    if (errors && errors.length > 0) {
      throw new Error(`Console errors: ${errors.join(', ')}`);
    }
  },
};
```

### Use Cases

#### 1. Validate No Console Errors

```javascript
async postVisit(page, context) {
  const errors = await page.evaluate(() => window.__errors);
  if (errors && errors.length > 0) {
    throw new Error(`Console errors: ${errors.join(', ')}`);
  }
}
```

**Requires**: Story sets up `window.__errors` array.

#### 2. Capture Screenshots

```javascript
async postVisit(page, context) {
  await page.screenshot({
    path: `screenshots/${context.id}.png`,
    fullPage: true,
  });
}
```

**Use case**: Visual regression testing with tools like Percy or Chromatic.

#### 3. Custom Assertions

```javascript
async postVisit(page, context) {
  // Check specific component types
  if (context.title.includes('Dialog')) {
    const is_visible = await page.isVisible('[role="dialog"]');
    if (!is_visible) {
      throw new Error('Dialog should be visible');
    }
  }
}
```

#### 4. Access Story Context

```javascript
async postVisit(page, context) {
  console.log('Story ID:', context.id);
  console.log('Story Title:', context.title);
  console.log('Story Name:', context.name);

  // Conditional logic based on story
  if (context.name === 'AfterUserClicksButton') {
    // Special validation
  }
}
```

## Console Error Tracking

Setup pattern for tracking console errors:

### In Component or Play Function

```javascript
// Track console errors
window.__errors = window.__errors || [];
const original_console_error = console.error;
console.error = (msg) => {
  window.__errors.push(msg);
  original_console_error('ERROR:', msg);
};
```

### In Post-Visit Hook

```javascript
async postVisit(page, context) {
  const errors = await page.evaluate(() => window.__errors);
  if (errors && errors.length > 0) {
    throw new Error(`Console errors: ${errors.join(', ')}`);
  }
}
```

## Combining Pre and Post Hooks

```javascript
module.exports = {
  async preVisit(page, context) {
    // Setup before story loads
    console.log(`Testing: ${context.title}`);
    await page.setViewportSize({ width: 1366, height: 768 });
  },

  async postVisit(page, context) {
    // Validate after story completes
    const errors = await page.evaluate(() => window.__errors);
    if (errors && errors.length > 0) {
      throw new Error(`Console errors: ${errors.join(', ')}`);
    }

    // Capture screenshot
    await page.screenshot({
      path: `screenshots/${context.id}.png`,
      fullPage: true,
    });
  },
};
```

## Advanced Patterns

### Conditional Screenshot Capture

Only capture on failure:

```javascript
async postVisit(page, context) {
  try {
    // Your validations
    const errors = await page.evaluate(() => window.__errors);
    if (errors && errors.length > 0) {
      throw new Error(`Console errors: ${errors.join(', ')}`);
    }
  } catch (error) {
    // Capture screenshot on failure
    await page.screenshot({
      path: `screenshots/failures/${context.id}.png`,
      fullPage: true,
    });
    throw error;  // Re-throw to fail test
  }
}
```

### Story-Specific Logic

Different behavior for different stories:

```javascript
async postVisit(page, context) {
  if (context.title.includes('Forms')) {
    // Validate form accessibility
    const has_labels = await page.$$eval('input', inputs =>
      inputs.every(input => input.labels.length > 0)
    );
    if (!has_labels) {
      throw new Error('All inputs must have labels');
    }
  }

  if (context.title.includes('Dialog')) {
    // Validate dialog is visible
    const is_visible = await page.isVisible('[role="dialog"]');
    if (!is_visible) {
      throw new Error('Dialog should be visible');
    }
  }
}
```

## Related Notes

- [Test Runner Config](/storybook/testing/test-runner-config.md) - Configuration settings
- [Test Runner Filtering](/storybook/testing/test-runner-filtering.md) - Ignoring stories
- [Play Functions](/storybook/play-functions/basics.md) - Story interactions
- [CI Artifacts](/storybook/testing/ci-cd-artifacts.md) - Uploading screenshots
