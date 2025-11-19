# Storybook Parameters Structure

Configuration options for Storybook story parameters.

## Layout Parameter

Choose layout based on component type:

```javascript
parameters: {
  layout: 'padded',      // Forms, cards, most components
  layout: 'centered',    // Small standalone widgets (DatePicker, Pagination)
  layout: 'fullscreen',  // Data tables, full-page components
}
```

## Story Description (Custom Banner)

Use `parameters.description` for story-level context displayed in canvas:

```javascript
parameters: {
  description: 'Happy path: User views their organization card. Most common scenario.',
}
```

See [Custom Description Banner](/storybook/documentation/custom-description-banner.md) for details.

## Viewport Parameters (Optional)

For responsive design testing:

```javascript
parameters: {
  viewport: {
    defaultViewport: 'mobile1',
  },
}
```

## Component Documentation

For comprehensive component documentation, create `.mdx` files instead of using autodocs.

See [MDX Component Docs](/storybook/documentation/mdx-component-docs.md) for details.

## Related Notes
- [Meta Object Structure](/storybook/meta-object-structure.md) - Basic meta object pattern
- [Story File Organization](/storybook/story-file-organization.md) - File structure order
- [Custom Description Banner](/storybook/documentation/custom-description-banner.md) - Story descriptions
- [MDX Component Docs](/storybook/documentation/mdx-component-docs.md) - Component documentation
