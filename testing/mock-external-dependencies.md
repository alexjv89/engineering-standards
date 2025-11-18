# Mock External Dependencies, Not Internal Implementation

Mock external APIs and services, but avoid mocking internal implementation details.

## Module-Level Mocking

Use `jest.mock()` to mock entire modules at the top of your test file:

```javascript
// Mock the module BEFORE importing it
jest.mock('@/utils/vimanaSVC', () => ({
  getDevices: jest.fn(),
  getAlerts: jest.fn(),
}));

// Then import after the mock
import * as vimanaSVC from '@/utils/vimanaSVC';

test('should fetch devices', async () => {
  // Configure mock return value
  vimanaSVC.getDevices.mockResolvedValue([
    { uuid: 'device1', name: 'Device 1' }
  ]);

  const result = await getDevices.execute({});

  expect(vimanaSVC.getDevices).toHaveBeenCalledWith({
    cookieStore: mockCookieStore,
    plant: mockPlantId,
  });
  expect(result).toHaveLength(1);
});
```

## Bad: Mock Internal Implementation

```javascript
// Don't do this - too coupled to implementation
import { UserComponent } from './UserComponent';
jest.spyOn(UserComponent.prototype, 'handleClick');

test('should call handleClick', () => {
  // This test breaks if implementation changes
  const component = render(<UserComponent />);
  component.find('button').click();
  expect(UserComponent.prototype.handleClick).toHaveBeenCalled();
});
```

## What to Mock

**Do mock:**
- External API calls
- Database queries (Sequelize, etc.)
- File system operations
- Network requests
- Third-party services
- Next.js functions (redirect, notFound, cookies)

**Don't mock:**
- Internal functions of the module being tested
- Implementation details
- Private methods

## Mocking Next.js Functions

```javascript
jest.mock('next/navigation', () => ({
  redirect: jest.fn(),
  notFound: jest.fn(),
}));

jest.mock('next/headers', () => ({
  cookies: jest.fn(),
}));

import { redirect } from 'next/navigation';
import { cookies } from 'next/headers';

test('should redirect unauthorized users', async () => {
  cookies.mockReturnValue({
    get: jest.fn().mockReturnValue(null),
  });

  await protectedPage();

  expect(redirect).toHaveBeenCalledWith('/login');
});
```

## Mocking Database Operations

```javascript
jest.mock('@/database', () => ({
  getDB: jest.fn()
}));

import { getDB } from '@/database';

const mockDB = {
  Statements: {
    findOne: jest.fn(),
    update: jest.fn()
  },
  Transactions: {
    bulkCreate: jest.fn()
  }
};

beforeEach(() => {
  jest.clearAllMocks();
  getDB.mockReturnValue(mockDB);
});

test('should save transactions', async () => {
  mockDB.Transactions.bulkCreate.mockResolvedValue([{}, {}]);

  await saveTransactions(transactions);

  expect(mockDB.Transactions.bulkCreate).toHaveBeenCalledWith(
    transactions,
    expect.any(Object)
  );
});
```

## Related Notes
- [Jest Mocking Patterns](./jest-mocking-patterns.md)
- [Semantic Mocking](./semantic-mocking.md)
- [Test Independence](./test-independence.md)
- [Setup and Teardown](./setup-teardown.md)
