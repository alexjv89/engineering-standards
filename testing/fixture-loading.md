# Fixture Loading: beforeAll Pattern for Expensive Operations

Use `beforeAll` to load expensive fixtures once, improving test performance.

## The Problem vs Solution

```javascript
// BAD: Loads file before EVERY test (slow!)
describe('Parser Tests', () => {
  it('should extract metadata', async () => {
    const statement = await loadMockStatement('large_file.pdf');
    const result = await parseStatement(statement);
    expect(result.metadata).toBeDefined();
  });
});

// GOOD: Load once, use in all tests
describe('ICICI Savings Parser', () => {
  let parsed_result;

  beforeAll(async () => {
    const statement = await loadMockStatement('baseline_56_txns.xls');
    parsed_result = await parseStatement(statement);
  });

  it('should extract complete metadata', () => {
    expect(parsed_result.metadata.acc_no).toBe('115901500651');
  });

  it('should extract all 56 transactions', () => {
    expect(parsed_result.transactions).toHaveLength(56);
  });
});
```

## When to Use

Use `beforeAll` for: Loading large files (PDFs, XLSX, CSV), database setup, expensive computations

Use `beforeEach` for: Resetting mocks, creating fresh instances, per-test isolation

## Multiple Fixtures

```javascript
describe('Parser Detection', () => {
  let hdfc_statement;
  let icici_statement;

  beforeAll(async () => {
    hdfc_statement = await loadMockStatement('hdfc.pdf');
    icici_statement = await loadMockStatement('icici.xlsx');
  });

  it('should detect HDFC parser', async () => {
    const result = await detectParser(hdfc_statement);
    expect(result.parser).toBe('hdfc_credit__pdf');
  });

  it('should detect ICICI parser', async () => {
    const result = await detectParser(icici_statement);
    expect(result.parser).toBe('icici_save__xlsx');
  });
});
```

## Combining beforeAll and beforeEach

```javascript
describe('Transaction Processing', () => {
  let statement; // Loaded once
  let mockDB;    // Reset each test

  beforeAll(async () => {
    statement = await loadMockStatement('statement.pdf');
  });

  beforeEach(() => {
    jest.clearAllMocks();
    mockDB = createMockDB();
  });

  it('should save transactions', async () => {
    await processStatement(statement, mockDB);
    expect(mockDB.save).toHaveBeenCalled();
  });
});
```

## Performance Impact

```
Without beforeAll: 5 tests × 500ms load = 2500ms
With beforeAll:    500ms + (5 × 10ms) = 550ms
```

5x faster test suite!

## Related Notes
- [Setup and Teardown](/testing/setup-teardown.md)
- [Test Independence](/testing/test-independence.md)
