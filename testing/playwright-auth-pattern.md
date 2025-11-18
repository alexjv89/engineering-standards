# Playwright Authentication Pattern

Set up authentication once and reuse across all E2E tests for faster execution.

## Authentication Setup File

```javascript
// e2e/auth.setup.js
import { test as setup, expect } from '@playwright/test';
import { login } from './helpers/authHelpers';

const authFile = '.auth/user.json';

const testCredentials = {
  tenant: 't1',
  plant: 'p1',
  username: 'test@example.com',
  password: 'password123',
};

setup('authenticate', async ({ page }) => {
  // Perform login
  await login(page, testCredentials);

  // Verify authentication succeeded
  await expect(page.locator('body')).not.toContainText('Sign In');

  // Save authentication state
  await page.context().storageState({ path: authFile });
});
```

## Login Helper Function

```javascript
// e2e/helpers/authHelpers.js
export async function login(page, credentials) {
  const { tenant, plant, username, password } = credentials;

  // Navigate to login page
  await page.goto(`/t/${tenant}/login`);

  // Fill in credentials
  await page.getByRole('textbox', { name: /email/i }).fill(username);
  await page.getByRole('textbox', { name: /password/i }).fill(password);

  // Submit form
  await page.getByRole('button', { name: /sign in/i }).click();

  // Wait for redirect after login
  await page.waitForURL(`**/${tenant}/${plant}/**`);
}

export async function logout(page) {
  await page.getByRole('button', { name: /logout/i }).click();
  await page.waitForURL('**/login');
}

export async function isAuthenticated(page) {
  const body = await page.locator('body').textContent();
  return !body.includes('Sign In');
}
```

## Using Stored Authentication

Configure Playwright to use stored state:

```javascript
// playwright.config.js
export default defineConfig({
  projects: [
    // Setup project (runs first)
    {
      name: 'setup',
      testMatch: /.*\.setup\.js/,
    },

    // Test projects (use stored auth)
    {
      name: 'chromium',
      use: {
        ...devices['Desktop Chrome'],
        storageState: '.auth/user.json', // Load stored auth
      },
      dependencies: ['setup'], // Depends on setup completing
    },
  ],
});
```

## Using in Tests

Tests automatically use stored authentication:

```javascript
// e2e/dashboard.spec.js
import { test, expect } from '@playwright/test';

// No need for beforeEach login - already authenticated!

test('displays user dashboard', async ({ page }) => {
  await page.goto('/t1/p1');

  // Already authenticated, no redirect to login
  expect(page.url()).toMatch(/\/t1\/p1/);
});
```

## When Login is Needed Per Test

For tests that need fresh login:

```javascript
import { login } from './helpers/authHelpers';

test.beforeEach(async ({ page }) => {
  await login(page, testCredentials);
});

test('test requiring fresh session', async ({ page }) => {
  // Test logic
});
```

## Multiple User Roles

Set up different auth files for different roles:

```javascript
// e2e/auth.admin.setup.js
const authFile = '.auth/admin.json';

setup('authenticate as admin', async ({ page }) => {
  await login(page, adminCredentials);
  await page.context().storageState({ path: authFile });
});

// playwright.config.js
{
  name: 'admin-tests',
  testMatch: /.*\.admin\.spec\.js/,
  use: {
    storageState: '.auth/admin.json',
  },
  dependencies: ['setup-admin'],
}
```

## Benefits

1. **Faster Tests** - Login once instead of before every test
2. **Reduced Flakiness** - Fewer login attempts = fewer failures
3. **Easier Debugging** - Authentication issues isolated to setup
4. **Multiple Roles** - Easy to test different user types

## Git Ignore

Add to `.gitignore`:

```
.auth/
playwright-report/
test-results/
```

## Related Notes
- [Playwright E2E Structure](./playwright-e2e-structure.md)
- [Playwright Helpers](./playwright-helpers.md)
