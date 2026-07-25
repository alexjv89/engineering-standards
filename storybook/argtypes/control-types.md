# Storybook ArgTypes Control Types

Control types available in Storybook argTypes.

## Text Input

```javascript
argTypes: {
  file_name: { control: 'text', description: 'Name of the uploaded file' },
}
```

## Select Dropdown

```javascript
argTypes: {
  variant: {
    control: 'select',
    options: ['plain', 'outlined', 'soft', 'solid'],
    description: 'Visual style variant',
  },
}
```

## Radio Buttons

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

```javascript
argTypes: {
  show_icon: { control: 'boolean', description: 'Whether to display icon' },
}
```

## Number Spinner

```javascript
argTypes: {
  page_count: {
    control: { type: 'number', min: 1, max: 100 },
    description: 'Total number of pages',
  },
}
```

## Range Slider

```javascript
argTypes: {
  opacity: {
    control: { type: 'range', min: 0, max: 1, step: 0.1 },
    description: 'Component opacity',
  },
}
```

## Object Editor

```javascript
argTypes: {
  data: { control: 'object', description: 'Array of data objects to display' },
}
```

## Color Picker

```javascript
argTypes: {
  background_color: { control: 'color', description: 'Background color' },
}
```

## Date Picker

```javascript
argTypes: {
  start_date: { control: 'date', description: 'Start date for the range' },
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
      type: { summary: 'string' },
    },
  },
}
```

## Action Callbacks

For event handler props:

```javascript
import { fn } from 'storybook/test';

const meta = {
  argTypes: {
    on_close: { action: 'closed', description: 'Callback when closed' },
  },
  args: {
    on_close: fn(),
  },
};
```

Both `action` in argTypes and `fn()` in args must be present for event handlers.

## Disabling Controls

Hide a control while keeping the prop documented:

```javascript
argTypes: {
  children: { control: false, description: 'Child components to render' },
}
```

## Related Notes
- [ArgTypes Basics](/storybook/argtypes/basics.md)
- [ArgTypes Examples](/storybook/argtypes/examples.md)
- [Action Mocking Pattern](/storybook/mocking/actions.md)
