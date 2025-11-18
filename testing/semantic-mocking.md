# Semantic Mocking: Implementation-Based Mock Behavior

Mock based on semantic meaning (URL, parameters) rather than call order for more maintainable tests.

## The Problem: Order-Dependent Mocks

```javascript
// BAD: Brittle - breaks if call order changes
mockFetch
  .mockResolvedValueOnce(mockDevices)    // First call
  .mockResolvedValueOnce(mockAlerts);     // Second call

// If code changes order, tests break
```

## The Solution: Semantic Mocking

Mock based on what's being called, not when:

```javascript
// GOOD: Mock based on URL pattern
fetchSVC.mockImplementation((options) => {
  if (options.url === '/api/v1/plant/device') {
    return Promise.resolve(mockDevices);
  }
  if (options.url === '/api/v3/alerts/alert') {
    return Promise.resolve(mockAlerts);
  }
  return Promise.resolve([]);
});

// Test is resilient to call order changes
const devices = await fetchSVC({ url: '/api/v1/plant/device' });
const alerts = await fetchSVC({ url: '/api/v3/alerts/alert' });
```

## Pattern Matching Examples

### URL-Based Mocking

```javascript
mockFetch.mockImplementation(({ url }) => {
  if (url.includes('/devices')) {
    return Promise.resolve(mockDevices);
  }
  if (url.includes('/alerts')) {
    return Promise.resolve(mockAlerts);
  }
  throw new Error(`Unexpected URL: ${url}`);
});
```

### Parameter-Based Mocking

```javascript
mockDB.Statements.findOne.mockImplementation((query) => {
  if (query.where.id === 'stmt-123') {
    return Promise.resolve(mockStatement123);
  }
  if (query.where.id === 'stmt-456') {
    return Promise.resolve(mockStatement456);
  }
  return Promise.resolve(null);
});
```

### Method-Based Mocking

```javascript
mockFetch.mockImplementation(({ method, url }) => {
  if (method === 'GET' && url === '/api/data') {
    return Promise.resolve({ data: [] });
  }
  if (method === 'POST' && url === '/api/data') {
    return Promise.resolve({ success: true });
  }
  return Promise.reject(new Error('Not implemented'));
});
```

## Benefits

1. **Resilient to Refactoring** - Code can change call order without breaking tests
2. **Self-Documenting** - Clear what each mock returns and why
3. **Easier Debugging** - Know exactly which condition matched
4. **Realistic** - Mirrors how real APIs behave

## When to Use

- Multiple calls to same function with different parameters
- Testing API clients that make various requests
- Complex integration tests
- Database query mocking

## Related Notes
- [Jest Mocking Patterns](./jest-mocking-patterns.md)
- [Mock External Dependencies](./mock-external-dependencies.md)
- [Test Independence](./test-independence.md)
