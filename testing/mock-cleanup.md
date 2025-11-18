# Mock Cleanup: jest.clearAllMocks()

Always reset mocks between tests to ensure test independence.

## The Pattern

Use `jest.clearAllMocks()` in `beforeEach` to reset all mock state:

```javascript
describe('API Service', () => {
  const mockFetch = jest.fn();

  beforeEach(() => {
    // Critical: Clear all mock history and implementations
    jest.clearAllMocks();

    // Set up fresh mock implementations
    mockFetch.mockResolvedValue({ data: [] });
  });

  it('should call API once', async () => {
    await fetchData();
    expect(mockFetch).toHaveBeenCalledTimes(1);
  });

  it('should call API once again', async () => {
    // mockFetch was reset, so call count starts at 0
    await fetchData();
    expect(mockFetch).toHaveBeenCalledTimes(1); // Passes!
  });
});
```

## Why It's Critical

Without `jest.clearAllMocks()`, tests become order-dependent:

```javascript
// BAD: No mock cleanup
describe('API Service', () => {
  const mockFetch = jest.fn();

  it('should call API once', async () => {
    await fetchData();
    expect(mockFetch).toHaveBeenCalledTimes(1); // Passes
  });

  it('should call API once again', async () => {
    await fetchData();
    expect(mockFetch).toHaveBeenCalledTimes(1); // FAILS! Count is 2
  });
});
```

## What jest.clearAllMocks() Clears

- Mock call counts (`toHaveBeenCalledTimes`)
- Mock call arguments (`toHaveBeenCalledWith`)
- Mock return values (but not mock implementations)
- Mock instances

## Other Cleanup Methods

```javascript
// Clear only call history (keeps implementation)
jest.clearAllMocks();

// Clear call history AND implementations
jest.resetAllMocks();

// Clear call history, implementations, AND restore original
jest.restoreAllMocks();
```

**Use `clearAllMocks` for most cases** - it preserves your mock setup while resetting call data.

## Pattern with Module Mocks

```javascript
jest.mock('@/utils/api', () => ({
  fetchData: jest.fn(),
}));

import * as api from '@/utils/api';

describe('Data Service', () => {
  beforeEach(() => {
    jest.clearAllMocks();
    // Re-setup mock implementations
    api.fetchData.mockResolvedValue({ data: [] });
  });

  it('should fetch data', async () => {
    await api.fetchData();
    expect(api.fetchData).toHaveBeenCalledTimes(1);
  });
});
```

## Related Notes
- [Setup and Teardown](./setup-teardown.md)
- [Test Independence](./test-independence.md)
- [Jest Mocking Patterns](./jest-mocking-patterns.md)
