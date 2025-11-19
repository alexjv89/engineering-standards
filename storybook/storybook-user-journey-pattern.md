# Storybook User Journey Pattern

Stories represent real user scenarios, not technical component variations.

## Philosophy

Each story answers: **"When/why does a user see this component?"**

Benefits:
- Product documentation - Stories document actual user scenarios
- Reduced cognitive load - Clear purpose for each story
- Better QA - Stories map to production bugs/scenarios

## Story Naming Convention

```javascript
// GOOD: User journey pattern
export const AfterSuccessfulUpload = { ... }
export const WhileFileIsProcessing = { ... }
export const WhenParserFails = { ... }
export const WithUnusualFileName = { ... }
export const DeveloperInteractive = { ... }

// BAD: Component variation pattern
export const Default = { ... }
export const EmptyState = { ... }
export const PDFFile = { ... }  // File type doesn't change UI
```

**Pattern:** `<UserAction/State>` or `With<EdgeCaseCondition>`

## Story Structure Template

```javascript
/**
 * USER JOURNEY STORY: After Successful Processing
 * When: File uploaded and processing completed
 * User sees: Complete metadata with all fields populated
 */
export const AfterSuccessfulProcessing = {
  args: {
    statement: {
      file_name: 'statement.pdf',
      parser_type: 'hdfc_credit_card__pdf',
      parsed_data: { transactions: [{}, {}] }
    }
  },
  play: autoOpenDropdown,
}
```

## Story Count Guidelines

- **Atoms** (buttons, inputs): 1-2 stories
- **Molecules** (cards, dialogs): 3-6 stories
- **Organisms** (forms, tables): 5-10 stories

## Typical Story Set (5-6 stories)

1. Happy Path - Most common scenario
2. Error/Edge States - Failures, missing data
3. Real Edge Cases - Production scenarios
4. DeveloperInteractive - Manual testing (required)

## Multi-User Pattern

When component behaves differently by user type, split files:

```
ComponentName.stories.js              // App user (default)
ComponentName.StaffUser.stories.js    // Staff/admin user
```

## Anti-Patterns

```javascript
// BAD: Data variations when UI doesn't change
export const PDFFile = { ... }
export const CSVFile = { ... }

// BAD: Technical state names
export const LocationNull = { ... }
export const IsLoadingTrue = { ... }

// GOOD: User-focused states
export const WhileFileIsProcessing = { ... }
export const WhenParserFails = { ... }
```

## Related Notes
- [Story Naming](/storybook/organization/story-naming.md) - Detailed naming conventions
- [Meta Object Structure](/storybook/organization/meta-object.md) - Story file configuration
- [Play Functions Basics](/storybook/play-functions/basics.md) - Automated interactions
