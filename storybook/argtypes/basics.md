# Storybook ArgTypes Basics

ArgTypes define the controls and documentation for component props in Storybook. This note covers when to include argTypes and their standard structure.

## Coverage Requirements

**Standard**: Comprehensive argTypes for complex components, minimal for UI primitives.

### Complex Components (Full ArgTypes)

Components with significant interaction or configuration should document all props:

- **Forms and inputs** (DatePicker, MultiSelect, FilterField)
- **Data display** (Table, TransactionsTable)
- **Dialogs and modals** (ExportConfirmDialog)
- **Composed components** (FileDropzone, PageHeader)

### UI Primitives (Minimal ArgTypes)

Simple visual components can have minimal argTypes:

- **Basic elements** (AlertBox, Pagination)
- **Static displays** (ErrorMessage)
- **Simple wrappers**

**Examples**:
- Complex: src/components/Table.stories.js:21-41 (4 props, full details)
- Minimal: src/components/AlertBox.stories.js:26-34 (2 props only)

## Standard ArgType Structure

```javascript
argTypes: {
  prop_name: {
    control: 'text' | 'select' | 'boolean' | 'number' | 'object',
    description: 'Human-readable description of the prop',
    options: ['option1', 'option2'],  // For select controls
    table: {
      defaultValue: { summary: 'default_value' },
    },
  },
}
```

## When to Omit ArgTypes

Skip argTypes for:

- Props that are always passed programmatically (rarely changed)
- Internal state props
- Complex callback props that can't be controlled
- Children/render props (better shown in stories)

## Naming Convention

Use `snake_case` for argType keys (matches your variable naming standard):

```javascript
// GOOD
argTypes: {
  file_name: { ... },
  on_change: { ... },
  show_icon: { ... },
}

// BAD
argTypes: {
  fileName: { ... },
  onChange: { ... },
  showIcon: { ... },
}
```

## Related Notes
- [ArgTypes Control Types](/storybook/argtypes/control-types.md) - Control type reference catalog
- [ArgTypes Examples](/storybook/argtypes/examples.md) - Real component examples
- [Action Mocking Pattern](/storybook/mocking/actions.md) - Server action callbacks
