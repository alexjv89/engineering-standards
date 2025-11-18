# Storybook User Journey Pattern

Stories represent real user scenarios and journeys, not technical component variations.

## Philosophy

Each story answers: **"When/why does a user see this component?"**

This approach provides:
- **Product documentation** - Stories document actual user scenarios
- **Reduced cognitive load** - Clear purpose for each story
- **Fast maintenance** - Easy to identify which scenarios need updating
- **Better QA** - Stories map directly to production bugs/scenarios

For complete pattern details, see: `/docs/design/storybook-user-journey-pattern.md` in consuming projects.

## Story Naming Convention

### Good Names (User Journey Pattern)

```javascript
export const AfterSuccessfulUpload = { ... }
export const WhileFileIsProcessing = { ... }
export const WhenParserFails = { ... }
export const WithUnusualFileName = { ... }
export const DeveloperInteractive = { ... }
```

**Pattern:** `<UserAction/State>` or `With<EdgeCaseCondition>`

### Bad Names (Component Variation Pattern)

```javascript
// Avoid these
export const Default = { ... }
export const EmptyState = { ... }
export const PDFFile = { ... }      // File type doesn't change UI
export const CSVFile = { ... }      // File type doesn't change UI
```

## Story Structure Template

```javascript
/**
 * USER JOURNEY STORY: After Successful Processing
 *
 * When: File uploaded and processing completed successfully
 *
 * User sees: Complete metadata with all fields populated
 *
 * Tests: Verify all data displays correctly
 */
export const AfterSuccessfulProcessing = {
  args: {
    statement: {
      id: 'stmt-456',
      file_name: 'hdfc_statement_jan_2024.pdf',
      parser_type: 'hdfc_credit_card__pdf',
      account: { id: 'acc-1', name: 'HDFC Credit Card' },
      parsed_data: { transactions: [{}, {}] }
    }
  },
  play: autoOpenDropdown,
  parameters: {
    docs: {
      description: {
        story: 'Most common scenario: Statement fully processed with all data available.'
      }
    }
  }
}
```

## Recommended Story Count by Component Type

- **Atoms** (buttons, inputs): 1-2 stories
- **Molecules** (cards, dialogs): 3-6 stories
- **Organisms** (forms, tables): 5-10 stories

## Typical Story Categories (5-6 stories)

1. **Happy Path** (1 story) - Most common scenario, auto-opens
2. **Error/Edge States** (1-2 stories) - Failures, missing data
3. **Real Edge Cases** (1-2 stories) - Scenarios from production
4. **Developer Testing** (1 story) - Manual interaction testing

## Example: FileDetailsDialog (6 stories)

```javascript
export const AfterSuccessfulUpload = { ... }      // Happy path
export const WhileFileIsProcessing = { ... }      // Temporary state
export const WhenParserFails = { ... }            // Error state
export const WithUnusualFileName = { ... }        // Edge case
export const WithVeryLargeFile = { ... }          // Edge case
export const DeveloperInteractive = { ... }       // Manual testing
```

## Multi-User Pattern: Splitting Stories by User Type

When component behaves differently for user types, split into separate files:

```
ComponentName.stories.js              // App user (default)
ComponentName.StaffUser.stories.js    // Staff/admin user
ComponentName.Premium.stories.js       // Premium user (if applicable)
```

Example titles:
```javascript
// File 1: StatementDropdownActions.stories.js
title: 'app/.../StatementDropdownActions/AppUser'

// File 2: StatementDropdownActions.StaffUser.stories.js
title: 'app/.../StatementDropdownActions/StaffUser'
```

## Anti-Patterns to Avoid

### Don't test every data variation
```javascript
// BAD: File type doesn't change the UI
export const PDFFile = { ... }
export const CSVFile = { ... }
export const XLSXFile = { ... }
```

### Don't duplicate with auto-open
```javascript
// BAD: Duplication
export const LongFileName = { ... }           // Manual click
export const AutoOpen_LongFileName = { ... }  // Same data, auto-opens
```

### Don't use technical state names
```javascript
// BAD: Technical, not user-focused
export const LocationNull = { ... }
export const ParserTypeNull = { ... }

// GOOD: User-focused
export const WhileFileIsProcessing = { ... }
export const WhenParserFails = { ... }
```

## Related Notes
- [Storybook Interactions](./storybook-interactions.md)
- [Storybook Mock Data](./storybook-mock-data.md)
