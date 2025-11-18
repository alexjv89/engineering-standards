# Playwright Helpers: Reusable Test Functions

Organize reusable helper functions by feature for maintainable E2E tests.

## Helper Organization

```
/e2e/helpers/
  ├── authHelpers.js        # Login, logout, auth checks
  ├── baseHelpers.js        # Generic utilities
  ├── dashboardHelpers.js   # Dashboard-specific actions
  └── chatHelpers.js        # Chat interface interactions
```

## Authentication Helpers

```javascript
// e2e/helpers/authHelpers.js

export async function login(page, credentials) {
  const { tenant, plant, username, password } = credentials;

  await page.goto(`/t/${tenant}/login`);
  await page.getByRole('textbox', { name: /email/i }).fill(username);
  await page.getByRole('textbox', { name: /password/i }).fill(password);
  await page.getByRole('button', { name: /sign in/i }).click();
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

## Base Helpers

```javascript
// e2e/helpers/baseHelpers.js

export async function waitForElement(page, selector, timeout = 5000) {
  await page.waitForSelector(selector, { timeout });
}

export async function isElementVisible(page, selector) {
  try {
    await page.waitForSelector(selector, { timeout: 2000 });
    return true;
  } catch {
    return false;
  }
}

export async function getTextContent(page, selector) {
  const element = await page.locator(selector);
  return await element.textContent();
}

export async function clickAndWait(page, selector, waitForSelector) {
  await page.click(selector);
  await page.waitForSelector(waitForSelector);
}
```

## Feature-Specific Helpers

```javascript
// e2e/helpers/dashboardHelpers.js

export async function navigateToDashboard(page, tenant, plant) {
  await page.goto(`/t/${tenant}/p/${plant}`);
  await page.waitForLoadState('networkidle');
}

export async function selectDevice(page, deviceName) {
  await page.getByText(deviceName).click();
  await page.waitForSelector('[data-testid="device-details"]');
}

export async function getDeviceStatus(page, deviceId) {
  const statusElement = page.locator(`[data-device-id="${deviceId}"] .status`);
  return await statusElement.textContent();
}
```

```javascript
// e2e/helpers/chatHelpers.js

export async function openChat(page) {
  await page.getByRole('button', { name: /chat/i }).click();
  await page.waitForSelector('[data-testid="chat-interface"]');
}

export async function sendMessage(page, message) {
  const input = page.getByRole('textbox', { name: /message/i });
  await input.fill(message);
  await page.getByRole('button', { name: /send/i }).click();
}

export async function waitForResponse(page, timeout = 30000) {
  await page.waitForSelector('[data-testid="assistant-message"]', { timeout });
}

export async function getLastMessage(page) {
  const messages = page.locator('[data-testid="assistant-message"]');
  const count = await messages.count();
  return await messages.nth(count - 1).textContent();
}
```

## Using Helpers in Tests

```javascript
// e2e/dashboard.spec.js
import { test, expect } from '@playwright/test';
import { login } from './helpers/authHelpers';
import { navigateToDashboard, selectDevice } from './helpers/dashboardHelpers';

test('should display device details', async ({ page }) => {
  await navigateToDashboard(page, 't1', 'p1');
  await selectDevice(page, 'Machine-01');

  const details = page.locator('[data-testid="device-details"]');
  await expect(details).toBeVisible();
});
```

## Benefits

1. **Reusability** - Share logic across multiple test files
2. **Maintainability** - Update action once, fixes all tests
3. **Readability** - Tests read like user stories
4. **Consistency** - Same actions performed same way everywhere
5. **Faster Development** - Build tests from composable actions

## Naming Conventions

- Use descriptive verb-based names: `navigateTo`, `selectDevice`, `sendMessage`
- Group by feature: `authHelpers`, `dashboardHelpers`, `chatHelpers`
- Prefix checks with `is`: `isAuthenticated`, `isVisible`
- Prefix getters with `get`: `getStatus`, `getTextContent`

## When to Create Helpers

Create helpers when:
- Action is repeated across multiple tests
- Action involves multiple steps
- Action represents common user flow
- Logic is complex and benefits from encapsulation

Don't create helpers for:
- One-off actions specific to single test
- Very simple single-line operations
- Actions that are clearer inline

## Related Notes
- [Playwright E2E Structure](./playwright-e2e-structure.md)
- [Playwright Auth Pattern](./playwright-auth-pattern.md)
