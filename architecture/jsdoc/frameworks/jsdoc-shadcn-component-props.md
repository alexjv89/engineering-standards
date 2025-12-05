# JSDoc: shadcn Component Props

Type shadcn/ui components using inline object types combined with React HTML attributes.

## Pattern

Use an inline object type intersected with `React.HTMLAttributes` to allow both custom props and standard HTML attributes:

```javascript
/**
 * @param {{
 *   className?: string,
 *   variant?: 'default' | 'secondary' | 'destructive' | 'outline',
 *   asChild?: boolean
 * } & React.HTMLAttributes<HTMLSpanElement>} props
 */
function Badge({ className, variant, asChild = false, ...props }) {
  // Implementation
}
```

## Why This Pattern

1. **Inline types** - No separate `@typedef` needed for simple, single-use prop definitions
2. **Multi-line formatting** - Keeps each prop on its own line for readability
3. **Intersection with HTML attributes** - Allows pass-through of `onClick`, `aria-*`, `style`, etc.
4. **Variant unions** - Documents allowed values directly in the type

## Choosing the Right HTMLAttributes

Match the underlying DOM element:

```javascript
// For span-based components (Badge, etc.)
React.HTMLAttributes<HTMLSpanElement>

// For button-based components
React.ButtonHTMLAttributes<HTMLButtonElement>

// For input-based components
React.InputHTMLAttributes<HTMLInputElement>

// For div-based components
React.HTMLAttributes<HTMLDivElement>
```

## When to Use @typedef Instead

Use a separate `@typedef` when:
- The type is reused across multiple functions
- The type is complex with many properties
- You need to export the type for external use

## Related Notes
- [JSDoc Basic Annotations](/architecture/jsdoc/syntax/jsdoc-basic-annotations.md)
- [JSDoc Generics](/architecture/jsdoc/syntax/jsdoc-generics.md)
- [Component Composition](/architecture/components/component-composition.md)
