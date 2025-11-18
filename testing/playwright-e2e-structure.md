# Playwright E2E Testing: Structure and Setup

Organize end-to-end tests with Playwright for realistic browser testing.

## Directory Structure

```
/
├── e2e/
│   ├── auth.setup.js          # Authentication setup
│   ├── helpers/
│   │   ├── authHelpers.js     # Login, logout, auth checks
│   │   ├── baseHelpers.js     # Generic utilities
│   │   ├── dashboardHelpers.js # Dashboard-specific actions
│   │   └── chatHelpers.js     # Chat interface interactions
│   ├── dashboard.spec.js      # Dashboard tests
│   ├── chat.spec.js           # Chat tests
│   └── ...                    # Other test files
├── playwright.config.js       # Playwright configuration
└── .auth/
    └── user.json              # Stored authentication state
```

## Playwright Configuration

```javascript
// playwright.config.js
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  fullyParallel: !process.env.CI, // Parallel locally, sequential in CI
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',

  use: {
    baseURL: 'http://127.0.0.1:3000',
    trace: 'on-first-retry',
  },

  projects: [
    // Setup project for authentication
    {
      name: 'setup',
      testMatch: /.*\.setup\.js/,
    },

    // Test projects
    {
      name: 'chromium',
      use: {
        ...devices['Desktop Chrome'],
        storageState: '.auth/user.json', // Use authenticated state
      },
      dependencies: ['setup'], // Run setup first
    },
    {
      name: 'firefox',
      use: {
        ...devices['Desktop Firefox'],
        storageState: '.auth/user.json',
      },
      dependencies: ['setup'],
    },
  ],

  webServer: {
    command: 'npm run dev',
    url: 'http://127.0.0.1:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

## Basic Test Structure

```javascript
// e2e/dashboard.spec.js
import { test, expect } from '@playwright/test';
import { login } from './helpers/authHelpers';

const testCredentials = {
  tenant: 't1',
  plant: 'p1',
  username: 'test@example.com',
  password: 'password123',
};

test.beforeEach(async ({ page }) => {
  await login(page, testCredentials);
});

test('loads dashboard successfully', async ({ page }) => {
  await page.goto('/t1/p1');
  await page.waitForLoadState('networkidle');

  expect(page.url()).not.toMatch(/\/login/);
  expect(page.url()).toMatch(/\/t1\/p1/);
});

test('displays device data', async ({ page }) => {
  await page.goto('/t1/p1');

  const deviceCard = page.locator('[data-testid="device-card"]').first();
  await expect(deviceCard).toBeVisible();
});
```

## Common Patterns

### Navigation

```javascript
await page.goto('/path');
await page.waitForLoadState('networkidle');
```

### Element Interactions

```javascript
// Click
await page.getByRole('button', { name: /submit/i }).click();

// Type
await page.getByRole('textbox', { name: 'Email' }).fill('test@example.com');

// Select
await page.selectOption('select#dropdown', 'option-value');
```

### Assertions

```javascript
// URL
expect(page.url()).toMatch(/\/dashboard/);
expect(page.url()).not.toMatch(/\/login/);

// Visibility
await expect(page.getByText('Welcome')).toBeVisible();
await expect(page.locator('[data-testid="card"]')).toBeVisible();

// Text content
await expect(page.locator('h1')).toHaveText('Dashboard');
await expect(page.locator('body')).not.toContainText('Sign In');
```

### Waiting

```javascript
// Wait for selector
await page.waitForSelector('[data-testid="loaded"]');

// Wait for network idle
await page.waitForLoadState('networkidle');

// Wait for timeout
await page.waitForTimeout(1000);
```

## When to Use E2E Tests

Use E2E tests for:
- **Critical user flows** (login, checkout, data submission)
- **Integration points** between multiple features
- **Real browser testing** (routing, redirects, auth)
- **Full-stack validation** (frontend + backend + database)

Don't use for:
- Unit-level logic (use Jest)
- Component interactions (use Storybook)
- Performance testing
- Extensive edge case coverage

## Related Notes
- [Playwright Auth Pattern](./playwright-auth-pattern.md)
- [Playwright Helpers](./playwright-helpers.md)
