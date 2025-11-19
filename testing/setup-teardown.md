# Setup and Teardown: beforeEach, afterEach

Use lifecycle hooks to avoid repetition in test setup and cleanup.

## Lifecycle Hooks

- `beforeAll()` - Runs once before all tests
- `beforeEach()` - Runs before each test
- `afterEach()` - Runs after each test
- `afterAll()` - Runs once after all tests

## Common Pattern

```javascript
describe('UserService', () => {
  let userService;
  let mockDb;

  beforeEach(() => {
    jest.clearAllMocks();
    mockDb = createMockDatabase();
    userService = new UserService(mockDb);
  });

  afterEach(() => {
    mockDb.clear();
  });

  it('should create user', async () => {
    const user = await userService.create({ name: 'John' });
    expect(user.name).toBe('John');
  });
});
```

## When to Use Each

**`beforeEach/afterEach`** - Test-specific setup/cleanup (creating instances, resetting mocks)

**`beforeAll/afterAll`** - Expensive one-time operations (database connections, loading fixtures)

## Database Integration Example

```javascript
describe('User API Integration', () => {
  beforeAll(async () => {
    await db.connect();
    await db.migrate.latest();
  });

  beforeEach(async () => {
    await db.seed.run();
  });

  afterAll(async () => {
    await db.destroy();
  });

  it('should create user', async () => {
    const user = await createUser({ name: 'John' });
    expect(user.id).toBeDefined();
  });
});
```

## Mock Cleanup Pattern

Always reset mocks to ensure test independence:

```javascript
describe('API Service', () => {
  const mockFetch = jest.fn();

  beforeEach(() => {
    jest.clearAllMocks();
    mockFetch.mockResolvedValue({ data: [] });
  });

  it('should call API', async () => {
    await fetchData();
    expect(mockFetch).toHaveBeenCalledTimes(1);
  });

  it('should call API again', async () => {
    await fetchData();
    expect(mockFetch).toHaveBeenCalledTimes(1); // Reset in beforeEach
  });
});
```

## Related Notes
- [Test Independence](/testing/test-independence.md)
- [Fixture Loading](/testing/fixture-loading.md)
- [AAA Pattern](/testing/aaa-pattern.md)
