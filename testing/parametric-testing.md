# Parametric Testing: test.each() Pattern

Use `test.each()` to run the same test with different data, reducing duplication.

## Basic Pattern

```javascript
test.each([
  ['input1', 'expected1'],
  ['input2', 'expected2'],
  ['input3', 'expected3'],
])('should handle %s correctly', (input, expected) => {
  const result = processInput(input);
  expect(result).toBe(expected);
});
```

## Testing Multiple Mock Files

```javascript
const mockFiles = [
  'hdfc_credit/__mocks__/baseline_45_txns.pdf',
  'hdfc_credit/__mocks__/annual_statement_large.pdf',
  'hdfc_credit/__mocks__/unusual_format_v2.pdf',
];

test.each(mockFiles)(
  'should detect HDFC Credit parser for %s',
  async (mockPath) => {
    const statement = await loadMockStatement(mockPath);
    const result = await detectBestParser(statement);

    expect(result.success).toBe(true);
    expect(result.best_parser).toBe('hdfc_credit__pdf');
  }
);
```

## Testing Operators/Conditions

```javascript
describe('Rule Engine Operators', () => {
  test.each([
    ['equals', 'test', 'test', true],
    ['equals', 'test', 'other', false],
    ['contains', 'hello world', 'world', true],
    ['contains', 'hello world', 'foo', false],
    ['greater_than', 100, 50, true],
    ['greater_than', 100, 150, false],
  ])(
    'operator "%s" with value %s and target %s should return %s',
    (operator, value, target, expected) => {
      const condition = { field: 'test_field', operator, value: target };
      const data = { test_field: value };

      const result = evaluateCondition(data, condition);

      expect(result).toBe(expected);
    }
  );
});
```

## Object-Based Parameters

For complex test data, use objects instead of arrays:

```javascript
test.each([
  {
    name: 'valid statement',
    file: 'valid.pdf',
    expected: { success: true },
  },
  {
    name: 'invalid format',
    file: 'invalid.pdf',
    expected: { success: false, error: 'Invalid format' },
  },
  {
    name: 'corrupted file',
    file: 'corrupted.pdf',
    expected: { success: false, error: 'Corrupted file' },
  },
])('should handle $name', async ({ file, expected }) => {
  const result = await parseStatement(file);
  expect(result).toEqual(expected);
});
```

## With describe.each()

Test multiple scenarios with nested test cases:

```javascript
describe.each([
  { bank: 'HDFC', format: 'pdf', parser: 'hdfc_credit__pdf' },
  { bank: 'ICICI', format: 'xlsx', parser: 'icici_save__xlsx' },
  { bank: 'SBI', format: 'csv', parser: 'sbi_save__csv' },
])('$bank $format Parser', ({ bank, format, parser }) => {
  it('should validate format', async () => {
    const valid = await validateFormat(parser);
    expect(valid).toBe(true);
  });

  it('should parse statement', async () => {
    const result = await parseWith(parser);
    expect(result.transactions).toBeDefined();
  });
});
```

## Benefits

1. **Reduce Duplication** - Write test logic once, run with many inputs
2. **Clear Test Matrix** - See all test cases at a glance
3. **Easy to Extend** - Add new test cases by adding data
4. **Better Failure Messages** - Jest shows which parameters failed

## When to Use

Use `test.each()` when:
- Testing same logic with different inputs
- Validating multiple edge cases
- Testing operator/condition matrices
- Running tests against multiple fixture files

Don't use when:
- Tests require different setup per case
- Test logic differs significantly between cases
- Only 1-2 test cases exist

## Related Notes
- [AAA Pattern](./aaa-pattern.md)
- [Test Utilities](./test-utilities.md)
- [Fixture Loading](./fixture-loading.md)
