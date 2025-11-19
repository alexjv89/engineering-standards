# Storybook File Header Pattern

Every story file should start with a JSDoc header explaining the user journey approach.

## Standard Header Template

```javascript
/**
 * ComponentName Stories - User Journey Pattern
 *
 * These stories represent real user scenarios/journeys rather than technical component variations.
 * Each story answers: "When/why does a user see this component?"
 *
 * Story naming convention: <UserAction/State>
 * Examples: AfterUploadingFile, BeforeSelectingDate, WhileProcessing
 */
```

## Purpose

The file header serves two key functions:

1. **Orients developers** - Immediately explains that stories follow user journey pattern (not component variation pattern)
2. **Sets expectations** - Shows the story naming convention with examples

## Real Examples from Codebase

All story files should include this header:

- src/components/FileDropzone.stories.js:1-9
- src/components/TransactionsTable.stories.js:1-9
- src/components/DatePicker.stories.js:1-9
- src/components/MultiSelect.stories.js:1-9
- src/components/AlertBox.stories.js:1-9
- src/components/Pagination.stories.js:1-9
- src/components/PageHeader.stories.js:1-9

## Customizing for Your Component

You can customize the examples to match your component's common scenarios:

```javascript
/**
 * TransactionsTable Stories - User Journey Pattern
 *
 * These stories represent real user scenarios/journeys.
 * Each story answers: "When/why does a user see this component?"
 *
 * Story naming convention: <UserAction/State>
 * Examples: AfterImportingTransactions, WhenNoDataExists, WithLargeDataset
 */
```

## What NOT to Include

Keep the header focused on orientation only. Do NOT include:

- Component-specific documentation (use `.mdx` files instead)
- Props documentation (use argTypes in meta)
- Implementation details (put in component file)

## Related Notes
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - Philosophy behind this pattern
- [Story Naming](/storybook/organization/story-naming.md) - Detailed naming conventions
- [Documentation Patterns](/storybook/documentation/documentation.md) - How this fits into the documentation strategy
