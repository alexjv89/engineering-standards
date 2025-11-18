# Jest Mocking Patterns: Module vs Function Mocks

Choose between module-level and function-level mocking based on your needs.

## Module-Level Mocking

Mock entire modules at the top of your test file (before imports):

```javascript
// Mock the entire module BEFORE importing
jest.mock('@/utils/vimanaSVC', () => ({
  getDevices: jest.fn(),
  getAlerts: jest.fn(),
}));

// Import AFTER mock definition
import * as vimanaSVC from '@/utils/vimanaSVC';

describe('Device Tools', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('should fetch devices', async () => {
    vimanaSVC.getDevices.mockResolvedValue([
      { uuid: 'device1', name: 'Device 1' }
    ]);

    const result = await getDevices.execute({});

    expect(vimanaSVC.getDevices).toHaveBeenCalled();
    expect(result).toHaveLength(1);
  });
});
```

**Use when:**
- Mocking external dependencies
- Mocking utility modules
- Need consistent mocking across all tests in file

## Function-Level Mocking

Create mock functions directly in your test:

```javascript
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

it('should save transactions', async () => {
  mockDB.Transactions.bulkCreate.mockResolvedValue([{}, {}]);

  await saveTransactions(transactions);

  expect(mockDB.Transactions.bulkCreate).toHaveBeenCalledTimes(1);
});
```

**Use when:**
- Creating complex mock objects (like database)
- Need different implementations per test
- Mocking configuration objects

## Mock Return Values

```javascript
// Simple value
mockFn.mockReturnValue(42);

// Async resolved value
mockFn.mockResolvedValue({ data: [] });

// Async rejected value
mockFn.mockRejectedValue(new Error('API failed'));

// Different values per call
mockFn
  .mockReturnValueOnce('first')
  .mockReturnValueOnce('second')
  .mockReturnValue('default');
```

## Mock Implementations

```javascript
// Custom implementation
mockFn.mockImplementation((arg) => {
  return arg * 2;
});

// Async implementation
mockFn.mockImplementation(async (id) => {
  return { id, name: 'Test' };
});

// Named mock (for better error messages)
const mockFetch = jest.fn().mockName('fetchSVC');
```

## Related Notes
- [Mock External Dependencies](./mock-external-dependencies.md)
- [Semantic Mocking](./semantic-mocking.md)
- [Mock Cleanup](./mock-cleanup.md)
- [Setup and Teardown](./setup-teardown.md)
