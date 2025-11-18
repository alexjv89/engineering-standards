# Test Utilities: Reusable Validators and Helpers

Create reusable test utilities to reduce duplication and ensure consistent validation.

## Test Utilities File Structure

Organize test utilities in a dedicated file:

```
/tests
  ├── parsers/
  │   ├── test-utils.js          # Shared utilities
  │   ├── icici_save.test.js     # Uses utilities
  │   └── hdfc_credit.test.js    # Uses utilities
```

## Validation Functions

Create reusable validators for complex objects:

```javascript
// tests/parsers/test-utils.js

export function validateTransaction(transaction, options = {}) {
  // Validate required fields
  expect(transaction).toHaveProperty('date');
  expect(typeof transaction.date).toBe('string');
  expect(transaction.date).toMatch(/^\d{4}-\d{2}-\d{2}$/);

  expect(transaction).toHaveProperty('particulars');
  expect(typeof transaction.particulars).toBe('string');

  // Validate numeric fields
  expect(typeof transaction.inflow).toBe('number');
  expect(transaction.inflow).toBeGreaterThanOrEqual(0);

  expect(typeof transaction.outflow).toBe('number');
  expect(transaction.outflow).toBeGreaterThanOrEqual(0);

  // Optional category validation
  if (options.requireCategory) {
    expect(transaction.category).toBeDefined();
  }
}

export function validateMetadata(metadata) {
  expect(metadata).toHaveProperty('acc_no');
  expect(metadata).toHaveProperty('currency');
  expect(metadata).toHaveProperty('opening_balance');
  expect(typeof metadata.opening_balance).toBe('number');
}

export function validateParserResult(result, options = {}) {
  // Validate top-level structure
  expect(result).toHaveProperty('metadata');
  expect(result).toHaveProperty('transactions');
  expect(Array.isArray(result.transactions)).toBe(true);

  // Use other validators
  validateMetadata(result.metadata);

  result.transactions.forEach(txn => {
    validateTransaction(txn, options);
  });
}
```

## Usage in Tests

```javascript
import { validateParserResult, validateTransaction } from './test-utils';

describe('ICICI Parser', () => {
  it('should parse statement correctly', async () => {
    const result = await parseStatement(statement);

    // Single line validation instead of dozens of assertions
    validateParserResult(result, { requireCategory: false });

    // Specific checks for first transaction
    const first_txn = result.transactions[0];
    expect(first_txn.date).toBe('2025-01-01');
    expect(first_txn.inflow).toBe(5000);
  });
});
```

## Fixture Loading Utilities

```javascript
// tests/parsers/test-utils.js
import fs from 'fs';
import path from 'path';

export async function loadMockStatement(filePath) {
  const fullPath = path.join(__dirname, filePath);
  const fileBuffer = fs.readFileSync(fullPath);

  // Determine file type
  const fileExtension = path.extname(filePath);

  return {
    id: 'mock-stmt-id',
    file_name: path.basename(filePath),
    file_type: fileExtension.slice(1),
    raw_data: await parseFile(fileBuffer, fileExtension)
  };
}
```

## Benefits

1. **Consistency** - Same validation logic across all tests
2. **Maintainability** - Update validation in one place
3. **Readability** - Test intent is clearer
4. **Reusability** - Share utilities across test files

## When to Create Utilities

Create test utilities when:
- Validating complex objects (transactions, API responses)
- Loading fixtures or mock data
- Setting up common test scenarios
- Performing repeated assertions

Don't create utilities for:
- One-off assertions
- Simple, straightforward checks
- Test-specific logic

## Related Notes
- [Test Data Factories](./test-data-factories.md)
- [Fixture Loading](./fixture-loading.md)
- [Test Independence](./test-independence.md)
