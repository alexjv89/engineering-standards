# Component Files: PascalCase.jsx

React component files use `PascalCase` with appropriate extension.

## Pattern

```
Button.jsx
UserCard.jsx
ChatInterface.jsx
NavigationMenu.jsx
```

## Extensions

- `.jsx` - React components with JSX
- `.js` - JavaScript without JSX (rare for components)

## File Structure

One component per file, file name matches component name:

```javascript
// Button.jsx
export default function Button({ children, onClick }) {
  return <button onClick={onClick}>{children}</button>;
}
```

## Test Files

Test files match the component name with `.test` or `.spec`:

```
Button.test.jsx
UserCard.spec.jsx
```

## Story Files

Storybook stories match component name with `.stories`:

```
Button.stories.jsx
UserCard.stories.jsx
```

## Complete Example

```
src/components/Button/
├── Button.jsx          # Main component
├── Button.test.jsx     # Tests
├── Button.stories.jsx  # Storybook stories
└── index.js            # Re-export
```

## Related Notes
- [Components: PascalCase](/naming/components-pascalcase.md)
- [CRUD Page Components: List/View/Create/Edit Prefix](/naming/files-crud-pages.md)
- [Component Composition](/architecture/components/component-composition.md)
- [Test File Naming](/naming/files-tests.md)
