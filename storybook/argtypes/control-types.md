# Storybook ArgTypes Control Types

Complete reference catalog of all control types available in Storybook argTypes.

## Text Input

For string props:

```javascript
argTypes: {
  file_name: {
    control: 'text',
    description: 'Name of the uploaded file',
  },
}
```

## Select Dropdown

For props with predefined options:

```javascript
argTypes: {
  variant: {
    control: 'select',
    options: ['plain', 'outlined', 'soft', 'solid'],
    description: 'Visual style variant',
  },
}
```

**Alternative syntax**:
```javascript
argTypes: {
  variant: {
    control: { type: 'select', options: ['plain', 'outlined', 'soft', 'solid'] },
    description: 'Visual style variant',
  },
}
```

## Radio Buttons

Similar to select but with radio UI:

```javascript
argTypes: {
  size: {
    control: 'radio',
    options: ['sm', 'md', 'lg'],
    description: 'Component size',
  },
}
```

## Boolean Checkbox

For boolean props:

```javascript
argTypes: {
  show_icon: {
    control: 'boolean',
    description: 'Whether to display the alert icon',
  },
}
```

## Number Spinner

For numeric props:

```javascript
argTypes: {
  page_count: {
    control: 'number',
    description: 'Total number of pages',
  },
}
```

**With range constraints**:
```javascript
argTypes: {
  page_count: {
    control: { type: 'number', min: 1, max: 100 },
    description: 'Total number of pages (1-100)',
  },
}
```

## Range Slider

For numeric props better controlled with a slider:

```javascript
argTypes: {
  opacity: {
    control: { type: 'range', min: 0, max: 1, step: 0.1 },
    description: 'Component opacity',
  },
}
```

## Object Editor

For complex object or array props:

```javascript
argTypes: {
  data: {
    control: 'object',
    description: 'Array of data objects to display',
  },
}
```

## Color Picker

For color props:

```javascript
argTypes: {
  background_color: {
    control: 'color',
    description: 'Background color of the component',
  },
}
```

## Date Picker

For date props:

```javascript
argTypes: {
  start_date: {
    control: 'date',
    description: 'Start date for the range',
  },
}
```

## Table Metadata

Document default values in the controls panel:

```javascript
argTypes: {
  size: {
    control: 'select',
    options: ['sm', 'md', 'lg'],
    description: 'Size of the component',
    table: {
      defaultValue: { summary: 'md' },
    },
  },
}
```

**With type information**:
```javascript
argTypes: {
  size: {
    control: 'select',
    options: ['sm', 'md', 'lg'],
    description: 'Size of the component',
    table: {
      defaultValue: { summary: 'md' },
      type: { summary: 'string' },
    },
  },
}
```

## Action Callbacks

For event handler props, use `action` and `fn()`:

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
  },
  args: {
    on_close: fn(),
    on_confirm: fn(),
  },
};
```

**Key details**:
- `action` in argTypes defines the action label in the Actions panel
- `fn()` in args creates a spy function that logs to the Actions panel
- Both must be present for event handlers

**Example**: src/components/ExportConfirmDialog.stories.js:61-77

## Disabling Controls

To hide a control while keeping the prop documented:

```javascript
argTypes: {
  children: {
    control: false,
    description: 'Child components to render',
  },
}
```

## Related Notes
- [ArgTypes Basics](/storybook/argtypes/basics.md) - When to use argTypes and structure
- [ArgTypes Examples](/storybook/argtypes/examples.md) - Real component examples
- [Action Mocking Pattern](/storybook/mocking/actions.md) - Server action patterns
- [Meta Object Structure](/storybook/organization/meta-object.md) - Story file configuration
