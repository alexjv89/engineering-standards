# Storybook ArgTypes Examples

Real-world examples of argTypes from actual components, demonstrating minimal to comprehensive coverage.

## Example 1: Table Component (Complex)

Full argTypes for a complex component with multiple visual options:

```javascript
argTypes: {
  type: {
    control: 'select',
    options: ['topHeader', 'regular'],
    description: 'Table variant with different header styles',
  },
  size: {
    control: 'select',
    options: ['sm', 'md', 'lg'],
    description: 'Size of table text and spacing',
  },
  variant: {
    control: 'select',
    options: ['plain', 'outlined', 'soft', 'solid'],
    description: 'Visual style variant',
  },
  color: {
    control: 'select',
    options: ['primary', 'neutral', 'danger', 'success', 'warning'],
    description: 'Color scheme',
  },
}
```

**Source**: src/components/Table.stories.js:21-41

**Pattern**: Complex component with 4 props, all select controls for predefined options.

## Example 2: DatePicker Component (With Callbacks)

ArgTypes with table metadata and action callbacks:

```javascript
import { fn } from '@storybook/test';

const meta = {
  argTypes: {
    default_value: {
      control: 'text',
      description: 'Pre-selected date in YYYY-MM-DD format',
      table: {
        defaultValue: { summary: 'undefined' },
      },
    },
    on_change: {
      action: 'date changed',
      description: 'Callback fired when date selection changes',
    },
    placeholder: {
      control: 'text',
      description: 'Placeholder text shown when no date is selected',
      table: {
        defaultValue: { summary: 'Select a date' },
      },
    },
  },
  args: {
    on_change: fn(),
  },
};
```

**Source**: src/components/DatePicker.stories.js:33-50

**Pattern**: Mixes text controls with action callbacks, includes default value documentation.

## Example 3: AlertBox Component (Minimal)

Simple component with only key props:

```javascript
argTypes: {
  color: {
    control: 'select',
    options: ['primary', 'neutral', 'danger', 'success', 'warning'],
    description: 'Color scheme of the alert',
  },
  show_icon: {
    control: 'boolean',
    description: 'Whether to display the alert icon',
  },
}
```

**Source**: src/components/AlertBox.stories.js:26-34

**Pattern**: UI primitive with only 2 props - visual variant and boolean toggle.

## Example 4: ExportConfirmDialog (Multiple Callbacks)

Component with multiple event handlers:

```javascript
import { fn } from '@storybook/test';

const meta = {
  argTypes: {
    on_close: {
      action: 'closed',
      description: 'Callback when dialog is closed',
    },
    on_confirm: {
      action: 'confirmed',
      description: 'Callback when export is confirmed',
    },
    file_name: {
      control: 'text',
      description: 'Name of the file being exported',
    },
  },
  args: {
    on_close: fn(),
    on_confirm: fn(),
    file_name: 'transactions.csv',
  },
};
```

**Source**: src/components/ExportConfirmDialog.stories.js:61-77

**Pattern**: Multiple action callbacks with `fn()` spies, mixed with regular controls.

## Example 5: MultiSelect (With Default Values)

Component with comprehensive table metadata:

```javascript
argTypes: {
  options: {
    control: 'object',
    description: 'Array of options to select from',
    table: {
      defaultValue: { summary: '[]' },
    },
  },
  placeholder: {
    control: 'text',
    description: 'Placeholder text when no options selected',
    table: {
      defaultValue: { summary: 'Select options...' },
    },
  },
  max_selections: {
    control: 'number',
    description: 'Maximum number of selections allowed',
    table: {
      defaultValue: { summary: 'undefined (unlimited)' },
    },
  },
}
```

**Pattern**: Object control for complex data, includes clear default value documentation.

## Common Patterns Summary

**Minimal Coverage** (2-3 props):
- Visual variants (color, size, variant)
- Key boolean toggles
- Example: AlertBox, Pagination

**Standard Coverage** (4-6 props):
- All visual options
- Primary data props
- Key callbacks
- Example: Table, DatePicker

**Comprehensive Coverage** (7+ props):
- All configurable props
- All event handlers
- Complex data structures
- Example: TransactionsTable, FilterField

## Related Notes
- [ArgTypes Basics](/storybook/argtypes/basics.md) - Coverage requirements and structure
- [ArgTypes Control Types](/storybook/argtypes/control-types.md) - Complete control type reference
- [Action Mocking Pattern](/storybook/mocking/actions.md) - Event handler patterns
- [Story Naming](/storybook/organization/story-naming.md) - User journey patterns
