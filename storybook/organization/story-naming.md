# Storybook Story Naming

Story names follow the User Journey pattern, describing when/why a user sees the component in this state.

## Naming Pattern

**Format**: `<UserAction/State>` or `With<EdgeCaseCondition>`

Each story name should answer: "When/why does a user see this?"

```javascript
// Action-based: User performed action or is in state
export const AfterImportingTransactions = { ... }
export const BeforeSelectingDate = { ... }
export const WhileUploading = { ... }

// Condition-based: Specific conditions or edge cases
export const WhenNoTransactionsExist = { ... }
export const WhenParserFails = { ... }
export const WithMissingCategories = { ... }
export const WithLargeDataset = { ... }
```

## Required Story: DeveloperInteractive

**Every story file must include `DeveloperInteractive`** for manual testing:

```javascript
/**
 * DEVELOPER TESTING STORY
 * Interactive story with no play functions. Use for manual testing.
 */
export const DeveloperInteractive = {
  args: { /* Minimal or realistic defaults */ },
};
```

## Anti-Patterns to Avoid

```javascript
// BAD: Technical implementation details
export const LocationNull = { ... }
export const IsLoadingTrue = { ... }

// BAD: Generic component variations
export const Default = { ... }
export const Primary = { ... }

// BAD: Data type variations when UI doesn't change
export const PDFFile = { ... }
export const CSVFile = { ... }

// GOOD: User-focused states
export const WhileFileIsProcessing = { ... }
export const WhenParserFails = { ... }
export const AfterSuccessfulUpload = { ... }
```

## Story Count Guidelines

- **Atoms** (buttons, inputs): 1-2 stories
- **Molecules** (cards, dialogs): 3-6 stories
- **Organisms** (forms, tables): 5-10 stories

## Typical Story Set (5-6 stories)

1. Happy Path - Most common scenario
2. Empty/Before State - Before interaction
3. Error State - When something fails
4. Edge Case(s) - Real production scenarios
5. DeveloperInteractive - Manual testing (required)

## Example: FileDropzone

```javascript
export const ReadyToUpload = { ... }              // Happy path
export const AfterSelectingFiles = { ... }        // Action result
export const AfterSelectingMultipleFiles = { ... }// Edge case
export const WhileUploading = { ... }             // Temporary state
export const WhenUploadFails = { ... }            // Error state
export const DeveloperInteractive = { ... }       // Manual testing
```

## Related Notes
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - Overall philosophy
- [Meta Object Structure](/storybook/organization/meta-object.md) - Story file configuration
