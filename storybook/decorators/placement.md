# Storybook Decorator Placement

Decorators can be applied at three levels: global, meta-level, and story-level. Each has specific use cases.

## Meta-Level Decorators

Apply to ALL stories in a file:

```javascript
const meta = {
  title: 'components/ComponentName',
  component: ComponentName,
  decorators: [
    // Applied to every story in this file
    (Story) => <div style={{ padding: '20px' }}><Story /></div>,
  ],
};

export default meta;
```

**When to use**:
- All stories in the file need the same environment
- Consistent layout constraints for all stories
- Provider that wraps all stories

**Most common use case** - This is where 90% of decorators should go.

## Story-Level Decorators

Apply to ONE specific story:

```javascript
export const SpecialCase = {
  decorators: [
    (Story) => <div style={{ background: '#f0f0f0' }}><Story /></div>,
  ],
  args: { ... },
};
```

**When to use**:
- One story needs different environment than others
- Testing edge cases with specific context
- Demonstrating component in unique layout

**Use sparingly** - Prefer meta-level for consistency.

## Global Decorators

Apply to ALL stories in the entire Storybook:

```javascript
// .storybook/preview.js
export const decorators = [
  (Story) => (
    <div style={{ fontFamily: 'Arial, sans-serif' }}>
      <Story />
    </div>
  ),
];
```

**When to use**:
- Global theme providers
- Base styling for entire Storybook
- Analytics or debugging wrappers

**Rare in most projects** - Use for truly universal needs.

## Decorator Composition Order

When multiple decorators are present, they compose from innermost to outermost:

```javascript
// Global decorator (outermost)
// ↓
// Meta decorator (middle)
// ↓
// Story decorator (innermost)
// ↓
// Component
```

**Example**:
```javascript
// Global: .storybook/preview.js
export const decorators = [
  (Story) => <ThemeProvider><Story /></ThemeProvider>,
];

// Meta-level: Component.stories.js
const meta = {
  decorators: [
    (Story) => <div style={{ padding: '20px' }}><Story /></div>,
  ],
};

// Story-level
export const SpecialCase = {
  decorators: [
    (Story) => <div style={{ background: 'blue' }}><Story /></div>,
  ],
};

// Renders as:
// <ThemeProvider>
//   <div style={{ padding: '20px' }}>
//     <div style={{ background: 'blue' }}>
//       <Component />
//     </div>
//   </div>
// </ThemeProvider>
```

## Multiple Decorators at Same Level

Array order matters - first decorator is outermost:

```javascript
const meta = {
  decorators: [
    // This wraps the second decorator
    (Story) => <QueryClientProvider><Story /></QueryClientProvider>,
    // This wraps the component
    (Story) => <div style={{ padding: '20px' }}><Story /></div>,
  ],
};

// Renders as:
// <QueryClientProvider>
//   <div style={{ padding: '20px' }}>
//     <Component />
//   </div>
// </QueryClientProvider>
```

## Combining Decorators and Parameters

Decorators and parameters work together:

```javascript
const meta = {
  parameters: {
    layout: 'fullscreen',  // Removes Storybook padding
  },
  decorators: [
    (Story) => (
      <div style={{ height: '100vh', padding: '20px' }}>
        <Story />
      </div>
    ),
  ],
};
```

**How they interact**:
- `layout: 'fullscreen'` removes Storybook's default padding (parameter)
- Decorator adds back controlled padding and height (decorator)

## Best Practices

1. **Default to meta-level** - Use for most decorators
2. **Limit story-level decorators** - Only for unique cases
3. **Be cautious with global** - Only for truly universal needs
4. **Order matters** - First in array = outermost wrapper
5. **Document composition** - Comment complex decorator chains

## Related Notes
- [Decorator Patterns](/storybook/decorators/patterns.md) - Common decorator patterns
- [Decorator Anti-Patterns](/storybook/decorators/anti-patterns.md) - What to avoid
- [Parameters Structure](/storybook/organization/parameters.md) - Alternative to decorators
