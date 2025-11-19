# Storybook: Mock Data Organization

Centralize mock data in JSON files for reusable, maintainable stories.

## Directory Structure

```
.storybook/
  └── mocks/
      ├── action.js           # Universal action proxy
      └── data/
          ├── accounts.json
          ├── org.json
          ├── members.json
          └── statements.json
```

## Centralized Mock Data

Store reusable mock data in JSON files:

```json
// .storybook/mocks/data/accounts.json
[
  {
    "id": "acc_1",
    "name": "HDFC Savings Account",
    "type": "bank",
    "category": "savings",
    "bank": "HDFC Bank",
    "number": "****1234"
  },
  {
    "id": "acc_2",
    "name": "ICICI Credit Card",
    "type": "credit_card",
    "bank": "ICICI Bank",
    "number": "****5678"
  }
]
```

## Using Mock Data in Stories

Import and use centralized data:

```javascript
import mockAccounts from '../../.storybook/mocks/data/accounts.json';

export const AfterSuccessfulProcessing = {
  args: {
    statement: {
      id: 'stmt-456',
      file_name: 'hdfc_statement_jan_2024.pdf',
      parser_type: 'hdfc_credit_card__pdf',
      account: mockAccounts[1], // Use mock data
      parsed_data: { transactions: [{}, {}] }
    }
  },
}
```

## Inline Mock Data for Edge Cases

For story-specific data, define inline:

```javascript
export const WithUnusualFileName = {
  args: {
    statement: {
      id: 'stmt-123',
      file_name: 'my_very_long_unusual_filename_that_might_break_the_ui_layout.pdf',
      parser_type: 'hdfc_credit__pdf',
      account: { id: 'acc-1', name: 'Test Account' },
    }
  },
}
```

## When to Use Each Approach

**Use centralized JSON** when:
- Data is reused across multiple stories
- Data represents common entities (accounts, users, etc.)
- Data should be consistent across stories

**Use inline data** when:
- Testing specific edge cases
- Data is story-specific
- Testing unusual values

## Benefits

1. **Centralized Data** - Mock data reused across stories
2. **Type Safety** - JSON files can be typed with JSDoc
3. **Easy Updates** - Change mock data in one place
4. **Consistency** - Same data across related stories

## Related Notes
- [Action Mocking Pattern](/storybook/mocking/actions.md)
- [User Type Mocking](/storybook/mocking/user-types.md)
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md)
