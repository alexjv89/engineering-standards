# What to Test

Guidance on test scope, what to test, and avoiding over-testing.

---

## Core Principle

**Test behavior, not implementation.**

Ask: "What does this code do?" not "How does this code work?"

---

## Test Scope by Code Type

### Utility Functions

Pure functions with clear input → output.

**Test:**
- Happy path with typical input
- Edge cases (empty, null, boundary values)
- Error cases (invalid input)

**Example:** `formatAmount(1234.5)` → `"$1,234.50"`

```javascript
describe('formatAmount', () => {
  it('should format positive numbers with commas and decimals', () => { });
  it('should handle zero', () => { });
  it('should handle negative numbers', () => { });
  it('should throw on non-numeric input', () => { });
});
```

**Typical count:** 3-6 tests

---

### Steps / Handlers

Functions that orchestrate business logic, often calling other services.

**Test:**
- Happy path — valid input produces expected output
- Output contract — structure matches what downstream expects
- Error handling — fails gracefully with clear errors
- Key business rules — logic that affects outcomes

**Don't test:**
- Internal helper functions through the step
- Every validation permutation
- Implementation details of called services (mock them)

**Example:** `send_email` step

```javascript
describe('send_email', () => {
  it('should send email and return success result', () => { });
  it('should return correct output structure', () => { });
  it('should throw when required config is missing', () => { });  // ONE test, not per-field
  it('should handle partial failure (some recipients fail)', () => { });
});
```

**Typical count:** 4-8 tests

---

### API Services / Data Fetchers

Functions that call external APIs and transform responses.

**Test:**
- Successful response handling
- Error response handling (4xx, 5xx)
- Data transformation correctness
- Retry/timeout behavior (if implemented)

**Example:** `fetchAccounts()`

```javascript
describe('fetchAccounts', () => {
  it('should return transformed accounts on success', () => { });
  it('should throw on API error', () => { });
  it('should filter by org when TXN_ORG_ID is set', () => { });
});
```

**Typical count:** 3-5 tests

---

### Data Transformers / Parsers

Functions that convert data from one format to another.

**Test:**
- Typical input produces expected output
- Edge cases in the data (missing fields, nulls)
- Invalid input handling

**Example:** `parseStatement()`

```javascript
describe('parseStatement', () => {
  it('should extract transactions from valid statement', () => { });
  it('should handle statement with no transactions', () => { });
  it('should throw on invalid format', () => { });
});
```

**Typical count:** 3-6 tests

---

## When to Use Parametric Testing

Use `test.each()` when testing the **same logic** with **different inputs**:

```javascript
// Good: Same validation logic, different invalid inputs
it.each([
  [{ subject: 'x', html: 'x' }, 'to'],
  [{ to: 'x', html: 'x' }, 'subject'],
  [{ to: 'x', subject: 'x' }, 'html'],
])('should throw when %s is missing', (config, missingField) => {
  await expect(sendEmail({ config })).rejects.toThrow(missingField);
});
```

**Don't use** when:
- Tests need different setup/assertions
- Only 1-2 cases exist
- It obscures what's being tested

---

## Signs of Over-Testing

| Symptom | Problem |
|---------|---------|
| Testing every permutation of input validation | Test one validation error, not every field |
| Testing internal helper functions through parent | Extract helper and test separately, or don't test |
| Many tests, low behavior coverage | Focus on outcomes, not code paths |
| Tests break when refactoring (but behavior unchanged) | Testing implementation, not behavior |
| Testing framework/library behavior | Trust your dependencies |

---

## Signs of Under-Testing

| Symptom | Problem |
|---------|---------|
| Only happy path tested | Add error/edge cases |
| No output structure validation | Add contract test |
| Complex business logic with 1-2 tests | Add tests for each business rule |
| Bugs found in production that tests should catch | Review test coverage for that area |

---

## Test Count Guidelines

| Code Type | Typical Tests | Focus |
|-----------|---------------|-------|
| Simple utility | 2-4 | Input/output, one edge case |
| Complex utility | 4-8 | Multiple edge cases, errors |
| Step/Handler | 4-8 | Behavior, contracts, errors |
| API service | 3-5 | Success, errors, transforms |
| Parser/Transformer | 3-6 | Valid, invalid, edge cases |

These are guidelines, not rules. A complex step with many business rules may need more tests. A simple pass-through may need fewer.

---

## Decision Framework

Before writing a test, ask:

1. **Does this test behavior or implementation?**
   - Behavior: "sends email to recipients" ✓
   - Implementation: "calls parseRecipients then loops" ✗

2. **Would this test break if I refactor without changing behavior?**
   - Yes → probably testing implementation
   - No → good test

3. **Is this testing my code or a dependency?**
   - Your code: test it
   - Library/framework behavior: trust it

4. **Is there already a test covering this path?**
   - Yes → don't duplicate
   - No → consider adding

---

## Example: Refactoring Over-Tested Code

**Before:** 19 tests for `send_email` step

```
config validation: 6 tests (one per field)
recipient parsing: 5 tests (implementation detail)
email sending: 2 tests
output structure: 3 tests
error handling: 3 tests
```

**After:** 6 tests focused on behavior

```javascript
describe('send_email', () => {
  it('should send email with correct parameters', () => { });
  it('should return correct output structure', () => { });
  it('should handle multiple recipients', () => { });
  it('should throw on invalid config', () => { });  // ONE test
  it('should throw when all recipients fail', () => { });
  it('should handle partial failure', () => { });
});
```

If `parseRecipients` needs thorough testing, extract to `utils/` and test separately.

---

## Related Notes

- [Unit vs Integration Tests](/testing/unit-vs-integration.md)
- [Parametric Testing](/testing/parametric-testing.md)
- [Mock External Dependencies](/testing/mock-external-dependencies.md)
- [Coverage Requirements](/testing/coverage-requirements.md)
