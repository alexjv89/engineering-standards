# Fixture Loading: beforeAll Pattern for Expensive Operations

Use `beforeAll` to load expensive fixtures once, improving test performance.

## The Problem: Repeated Expensive Operations

```javascript
// BAD: Loads file before EVERY test (slow!)
describe('Parser Tests', () => {
  it('should extract metadata', async () => {
    const statement = await loadMockStatement('large_file.pdf'); // Load
    const result = await parseStatement(statement);
    expect(result.metadata).toBeDefined();
  });

  it('should extract transactions', async () => {
    const statement = await loadMockStatement('large_file.pdf'); // Load again!
    const result = await parseStatement(statement);
    expect(result.transactions).toHaveLength(56);
  });
});
```

## The Solution: Load Once with beforeAll

```javascript
// GOOD: Load once, use in all tests
describe('ICICI Savings Parser - baseline_56_txns.xls', () => {
  let parsed_result;

  beforeAll(async () => {
    // Load expensive fixture ONCE
    const statement = await loadMockStatement('icici_save/__mocks__/baseline_56_txns.xls');
    parsed_result = await parseStatement(statement);
  });

  it('should extract complete metadata', () => {
    const { metadata } = parsed_result;
    expect(metadata.acc_no).toBe('115901500651');
    expect(metadata.currency).toBe('INR');
  });

  it('should extract all 56 transactions', () => {
    expect(parsed_result.transactions).toHaveLength(56);
  });

  it('should extract first transaction correctly', () => {
    const first_txn = parsed_result.transactions[0];
    expect(first_txn.date).toBe('2025-07-01');
    expect(first_txn.particulars).toBe('INF/IWISH CONTRIBUTION');
    expect(first_txn.outflow).toBe(1000);
  });
});
```

## When to Use beforeAll

Use `beforeAll` for:
- Loading large fixture files (PDFs, XLSX, CSV)
- Database setup/seeding
- Expensive computations used by all tests
- One-time API calls for test data

Use `beforeEach` for:
- Resetting mocks
- Creating fresh test instances
- Operations that must be isolated per test

## Pattern: Multiple Fixtures

```javascript
describe('Parser Detection', () => {
  let hdfc_statement;
  let icici_statement;
  let sbi_statement;

  beforeAll(async () => {
    // Load all fixtures once
    hdfc_statement = await loadMockStatement('hdfc.pdf');
    icici_statement = await loadMockStatement('icici.xlsx');
    sbi_statement = await loadMockStatement('sbi.csv');
  });

  it('should detect HDFC parser', async () => {
    const result = await detectParser(hdfc_statement);
    expect(result.parser).toBe('hdfc_credit__pdf');
  });

  it('should detect ICICI parser', async () => {
    const result = await detectParser(icici_statement);
    expect(result.parser).toBe('icici_save__xlsx');
  });

  it('should detect SBI parser', async () => {
    const result = await detectParser(sbi_statement);
    expect(result.parser).toBe('sbi_save__csv');
  });
});
```

## Combining beforeAll and beforeEach

```javascript
describe('Transaction Processing', () => {
  let statement; // Loaded once
  let mockDB;    // Reset each test

  beforeAll(async () => {
    // Expensive: Load fixture once
    statement = await loadMockStatement('statement.pdf');
  });

  beforeEach(() => {
    // Fast: Reset mocks before each test
    jest.clearAllMocks();
    mockDB = createMockDB();
  });

  it('should save transactions', async () => {
    await processStatement(statement, mockDB);
    expect(mockDB.save).toHaveBeenCalled();
  });
});
```

## Cleanup with afterAll

```javascript
describe('Database Integration', () => {
  beforeAll(async () => {
    await db.connect();
    await db.migrate.latest();
  });

  afterAll(async () => {
    // Clean up expensive resources
    await db.destroy();
  });

  it('should query data', async () => {
    const result = await db.query('SELECT * FROM users');
    expect(result).toBeDefined();
  });
});
```

## Performance Benefits

```
Without beforeAll: 5 tests × 500ms load time = 2500ms
With beforeAll:    500ms load time + (5 tests × 10ms) = 550ms

5x faster test suite!
```

## Related Notes
- [Setup and Teardown](./setup-teardown.md)
- [Test Utilities](./test-utilities.md)
- [Test Independence](./test-independence.md)
