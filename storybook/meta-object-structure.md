# Storybook Meta Object Structure

Standard structure for the meta object in Storybook story files using CSF v3.

## Standard Pattern

```javascript
const meta = {
  title: 'components/ComponentName',
  component: ComponentName,
  parameters: {
    layout: 'padded',
  },
  argTypes: {
    // See argtypes-basics.md
  },
  decorators: [
    // See decorator-patterns.md
  ],
};

export default meta;
```

## Export Style

**Standard**: Always use the `const meta` variable pattern

```javascript
// GOOD: Clear and explicit
const meta = {
  title: 'components/Table',
  component: Table,
  parameters: { ... },
};

export default meta;
```

```javascript
// AVOID: Inline export
export default {
  title: 'components/Table',
  component: Table,
};
```

**Rationale**: The `const meta` pattern makes it easier to reference metadata within the file and is more maintainable.

## Autodocs

**Note**: We do NOT use Storybook's autodocs feature. For component-level documentation, create `.mdx` files (see [MDX Component Docs](/storybook/documentation/mdx-component-docs.md)).

## Related Notes
- [Story Title Convention](/storybook/story-title-convention.md) - Title naming and hierarchy
- [Parameters Structure](/storybook/parameters-structure.md) - Layout and description parameters
- [Story File Organization](/storybook/story-file-organization.md) - File structure order
- [ArgTypes Basics](/storybook/argtypes-basics.md) - Props documentation coverage
- [Decorator Patterns](/storybook/decorator-patterns.md) - Layout and context providers
