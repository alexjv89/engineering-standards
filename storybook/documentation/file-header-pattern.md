# File Header Pattern

JSDoc headers at the top of story files orient developers to the user journey pattern.

## Purpose

Every `.stories.js` file should have a header comment explaining:
- This file uses the user journey pattern
- Stories represent real user scenarios
- Each story answers "When/why does a user see this?"

## Pattern

```javascript
/**
 * ComponentName Stories - User Journey Pattern
 *
 * These stories represent real user scenarios/journeys.
 * Each story answers: "When/why does a user see this component?"
 *
 * Stories:
 * - AfterSuccessfulUpload: User uploaded file, processing complete
 * - WhileProcessing: File upload in progress
 * - WhenParserFails: Processing failed with error
 */
import { ComponentName } from './ComponentName';

export default {
  title: 'Components/ComponentName',
  component: ComponentName,
};

export const AfterSuccessfulUpload = { ... };
```

## What to Include

**Always include:**
- Component name
- "User Journey Pattern" label
- Brief explanation of the pattern

**Optionally include:**
- List of stories with one-line descriptions
- Any important context about the component

## Benefits

- **Discoverability** - Developers know this file follows user journey pattern
- **Documentation** - Quick overview of all stories in file
- **Consistency** - Standard format across all story files

## Related Notes
- [Documentation Strategy](/storybook/documentation/documentation.md) - Three-layer documentation approach
- [Custom Description Banner](/storybook/documentation/custom-description-banner.md) - Story-level descriptions
- [Story Naming](/storybook/organization/story-naming.md) - User journey naming conventions
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - Overall philosophy
