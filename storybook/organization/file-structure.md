# Storybook Story File Organization

Consistent file structure order for Storybook story files.

## Standard File Order

```javascript
// 1. Imports
import ComponentName from './ComponentName';
import { OtherComponent } from '@mui/joy';
import { within, userEvent, expect } from '@storybook/test';

// 2. Sample/Mock Data (if needed)
const sample_transactions = [
  { id: 1, amount: 100 },
  { id: 2, amount: 200 },
];

// 3. Meta Object
const meta = {
  title: 'components/ComponentName',
  component: ComponentName,
  parameters: { ... },
  argTypes: { ... },
  decorators: [ ... ],
};

export default meta;

// 4. Stories (parameters before args)
export const AfterImportingData = {
  parameters: {
    description: 'Happy path: User views data after import. Table shows all transactions.',
  },
  args: { data: sample_transactions },
};

export const DeveloperInteractive = {
  args: {},
};
```

## Story Object Order

Within each story, always put `parameters` before `args`:

```javascript
export const StoryName = {
  parameters: {
    description: 'Story description...',
  },
  args: {
    prop1: 'value',
  },
};
```

**Rationale**: Consistent ordering improves readability and maintainability across all story files.

## Related Notes
- [Meta Object Structure](/storybook/organization/meta-object.md) - Basic meta object pattern
- [Story Title Convention](/storybook/organization/title-convention.md) - Title naming
- [Story Naming](/storybook/organization/story-naming.md) - User journey naming conventions
- [Parameters Structure](/storybook/organization/parameters.md) - Layout and description parameters
