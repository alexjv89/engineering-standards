# Testing Practices

This document defines testing standards, patterns, and best practices for ensuring code quality and reliability.

## Table of Contents
- [Testing Philosophy](#testing-philosophy)
- [Test Types & Strategy](#test-types--strategy)
- [Test Structure](#test-structure)
- [Naming Conventions](#naming-conventions)
- [Coverage Requirements](#coverage-requirements)
- [Best Practices](#best-practices)

## Testing Philosophy

### Core Principles

1. **Tests are documentation** - Tests should clearly describe what the code does
2. **Test behavior, not implementation** - Focus on what, not how
3. **Fast feedback** - Tests should run quickly to enable rapid iteration
4. **Reliable** - Tests should not be flaky; they pass or fail consistently
5. **Maintainable** - Tests should be easy to understand and update

### Testing Pyramid

Follow the testing pyramid for balanced coverage:
```
        /\
       /  \  E2E Tests (Few)
      /____\
     /      \
    / Integ. \ Integration Tests (Some)
   /__________\
  /            \
 /  Unit Tests  \ Unit Tests (Many)
/________________\
```

- **70%** Unit tests
- **20%** Integration tests
- **10%** E2E tests

## Test Types & Strategy

### Unit Tests

**Purpose**: Test individual functions, components, or modules in isolation

**When to write:**
- For utility functions
- For business logic
- For individual React components
- For API service functions

**Example:**
```javascript
// utils/calculateTotal.test.js
import { calculateTotal } from './calculateTotal';

describe('calculateTotal', () => {
  it('should calculate total with tax', () => {
    const result = calculateTotal(100, 0.08);
    expect(result).toBe(108);
  });

  it('should handle zero tax rate', () => {
    const result = calculateTotal(100, 0);
    expect(result).toBe(100);
  });

  it('should throw error for negative prices', () => {
    expect(() => calculateTotal(-100, 0.08)).toThrow('Price cannot be negative');
  });
});
```

### Integration Tests

**Purpose**: Test how multiple units work together

**When to write:**
- For API endpoint workflows
- For database operations
- For component interactions
- For service layer integration

**Example:**
```javascript
// api/users.integration.test.js
import { createUser, getUserById } from './users';
import { db } from '../database';

describe('User API Integration', () => {
  beforeAll(async () => {
    await db.migrate.latest();
  });

  afterAll(async () => {
    await db.destroy();
  });

  it('should create and retrieve user', async () => {
    const userData = { name: 'John', email: 'john@example.com' };
    const created = await createUser(userData);

    const retrieved = await getUserById(created.id);
    expect(retrieved.name).toBe('John');
    expect(retrieved.email).toBe('john@example.com');
  });
});
```

### End-to-End (E2E) Tests

**Purpose**: Test complete user workflows in a production-like environment

**When to write:**
- For critical user journeys
- For complex multi-step workflows
- For cross-system integration

**Example (Playwright):**
```javascript
// e2e/user-registration.spec.js
import { test, expect } from '@playwright/test';

test('user can register and login', async ({ page }) => {
  // Navigate to registration
  await page.goto('/register');

  // Fill registration form
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="password"]', 'SecurePass123');
  await page.click('button[type="submit"]');

  // Verify redirect to dashboard
  await expect(page).toHaveURL('/dashboard');
  await expect(page.locator('h1')).toContainText('Welcome');
});
```

### Visual Regression Tests (Storybook)

**Purpose**: Catch unintended visual changes in components

**When to write:**
- For all UI components
- For different component states
- For responsive layouts

**Example:**
```javascript
// Button.stories.jsx
export default {
  title: 'Components/Button',
  component: Button,
};

export const Primary = {
  args: {
    variant: 'primary',
    children: 'Click me',
  },
};

export const Disabled = {
  args: {
    variant: 'primary',
    disabled: true,
    children: 'Disabled',
  },
};

export const Loading = {
  args: {
    variant: 'primary',
    isLoading: true,
    children: 'Loading',
  },
};
```

## Test Structure

### Arrange-Act-Assert (AAA) Pattern

Structure all tests using the AAA pattern:

```javascript
test('should add item to cart', () => {
  // Arrange - Set up test data and conditions
  const cart = new ShoppingCart();
  const item = { id: 1, name: 'Widget', price: 10 };

  // Act - Perform the action being tested
  cart.addItem(item);

  // Assert - Verify the expected outcome
  expect(cart.items).toHaveLength(1);
  expect(cart.items[0]).toEqual(item);
  expect(cart.total).toBe(10);
});
```

### Test Organization

**Group related tests** using `describe` blocks:

```javascript
describe('ShoppingCart', () => {
  describe('addItem', () => {
    it('should add item to empty cart', () => { });
    it('should increment quantity for existing item', () => { });
    it('should throw error for invalid item', () => { });
  });

  describe('removeItem', () => {
    it('should remove item from cart', () => { });
    it('should handle removing non-existent item', () => { });
  });

  describe('calculateTotal', () => {
    it('should calculate total for single item', () => { });
    it('should calculate total for multiple items', () => { });
    it('should apply discount correctly', () => { });
  });
});
```

### Setup and Teardown

Use lifecycle hooks to avoid repetition:

```javascript
describe('UserService', () => {
  let userService;
  let mockDb;

  beforeEach(() => {
    // Setup runs before each test
    mockDb = createMockDatabase();
    userService = new UserService(mockDb);
  });

  afterEach(() => {
    // Cleanup runs after each test
    mockDb.clear();
  });

  it('should create user', async () => {
    const user = await userService.create({ name: 'John' });
    expect(user.name).toBe('John');
  });
});
```

## Naming Conventions

### Test Files

- **Co-located with source**: `ComponentName.test.js` next to `ComponentName.js`
- **Alternative**: `__tests__/ComponentName.test.js`
- **E2E tests**: `e2e/feature-name.spec.js`

### Test Descriptions

Use clear, descriptive test names that read like sentences:

```javascript
// Good - Describes behavior clearly
it('should show error message when email is invalid', () => { });
it('should disable submit button while form is submitting', () => { });
it('should redirect to login page when user is not authenticated', () => { });

// Avoid - Too vague or technical
it('works', () => { });
it('test email validation', () => { });
it('handles edge case', () => { });
```

### Describe Blocks

Use noun phrases for `describe` and verb phrases for `it`:

```javascript
describe('LoginForm', () => {  // Noun - what you're testing
  it('validates email format', () => { });  // Verb - what it should do
  it('shows error for wrong password', () => { });
  it('redirects after successful login', () => { });
});
```

## Coverage Requirements

### Minimum Coverage Targets

- **Unit tests**: 80% coverage minimum
- **Critical paths**: 100% coverage required
- **Edge cases**: Must be explicitly tested

### What to Prioritize

**Must test (100% coverage):**
- Business logic and calculations
- Authentication and authorization
- Payment processing
- Data transformations
- API request/response handling

**Should test (80%+ coverage):**
- UI components
- Utility functions
- Database queries
- Error handling

**Can skip:**
- Third-party library wrappers (if thin)
- Simple getters/setters
- Configuration files

### Coverage Metrics

Track these metrics:
- **Line coverage**: Percentage of lines executed
- **Branch coverage**: Percentage of branches executed
- **Function coverage**: Percentage of functions called

**Configuration (Jest):**
```javascript
// jest.config.js
module.exports = {
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.stories.{js,jsx}',
    '!src/**/*.test.{js,jsx}',
  ],
  coverageThresholds: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
};
```

## Best Practices

### Mocking & Stubbing

**Mock external dependencies**, not internal implementation:

```javascript
// Good - Mock external API
import { fetchUserData } from './api';
jest.mock('./api');

test('should display user data', async () => {
  fetchUserData.mockResolvedValue({ name: 'John' });
  // Test component using mocked API
});

// Avoid - Mocking internal details
import { UserComponent } from './UserComponent';
jest.spyOn(UserComponent.prototype, 'handleClick'); // Too coupled to implementation
```

**Use MSW for API mocking:**
```javascript
import { rest } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  rest.get('/api/users/:id', (req, res, ctx) => {
    return res(ctx.json({ id: req.params.id, name: 'John' }));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

### Test Data Management

**Use factories or fixtures** for test data:

```javascript
// testUtils/factories.js
export function createUser(overrides = {}) {
  return {
    id: Math.random().toString(),
    name: 'Test User',
    email: 'test@example.com',
    role: 'user',
    ...overrides,
  };
}

// In tests
import { createUser } from './testUtils/factories';

test('should promote user to admin', () => {
  const user = createUser({ role: 'user' });
  const admin = promoteToAdmin(user);
  expect(admin.role).toBe('admin');
});
```

### Async Testing

**Always use async/await** for async tests:

```javascript
// Good
test('should fetch user data', async () => {
  const data = await fetchUserData('123');
  expect(data.name).toBe('John');
});

// Avoid - Using callbacks or .then()
test('should fetch user data', (done) => {
  fetchUserData('123').then(data => {
    expect(data.name).toBe('John');
    done();
  });
});
```

### Testing React Components

**Use React Testing Library**, not Enzyme:

```javascript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

test('should submit form with user input', async () => {
  const onSubmit = jest.fn();
  render(<LoginForm onSubmit={onSubmit} />);

  // Interact as user would
  await userEvent.type(screen.getByLabelText('Email'), 'test@example.com');
  await userEvent.type(screen.getByLabelText('Password'), 'password123');
  await userEvent.click(screen.getByRole('button', { name: /submit/i }));

  await waitFor(() => {
    expect(onSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123',
    });
  });
});
```

### Error Testing

**Test error cases explicitly:**

```javascript
test('should handle network errors gracefully', async () => {
  // Mock API to fail
  fetchUserData.mockRejectedValue(new Error('Network error'));

  render(<UserProfile userId="123" />);

  // Verify error state
  await waitFor(() => {
    expect(screen.getByText(/failed to load/i)).toBeInTheDocument();
  });
});
```

### Test Independence

**Each test should be independent:**

```javascript
// Good - Each test has its own setup
describe('TodoList', () => {
  it('should add todo', () => {
    const list = new TodoList();
    list.add('Buy milk');
    expect(list.items).toHaveLength(1);
  });

  it('should remove todo', () => {
    const list = new TodoList();
    list.add('Buy milk');
    list.remove(0);
    expect(list.items).toHaveLength(0);
  });
});

// Avoid - Tests depend on each other
describe('TodoList', () => {
  const list = new TodoList();  // Shared state

  it('should add todo', () => {
    list.add('Buy milk');
    expect(list.items).toHaveLength(1);  // Passes
  });

  it('should remove todo', () => {
    list.remove(0);
    expect(list.items).toHaveLength(0);  // Depends on previous test
  });
});
```

## Continuous Integration

Tests must pass before code can be merged:

```yaml
# .github/workflows/test.yml
name: Test
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      - run: npm ci
      - run: npm test -- --coverage
      - run: npm run test:e2e
```

## Test Maintenance

- **Update tests when requirements change**
- **Delete tests for removed features**
- **Refactor tests when they become unclear**
- **Review flaky tests and fix or remove them**

---

**See Also:**
- [Code Style Guide](code-style.md)
- [Development Workflows](development-workflows.md)
