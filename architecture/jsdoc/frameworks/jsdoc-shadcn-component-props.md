# JSDoc: shadcn Component Props

Type shadcn/ui components using inline object types combined with React HTML attributes.

## When to Use Inline Style

Use inline object types when props are **self-documenting UI/framework conventions**:
- `className`, `variant`, `size`, `disabled`, `asChild`
- Standard HTML attributes (`onClick`, `aria-*`, `style`)
- Props where the name alone conveys the meaning

For **business logic props** that need descriptions, use [nested property style](/architecture/jsdoc/types/jsdoc-function-level-types.md) instead.

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

1. **Self-documenting props** - UI conventions (`className`, `variant`) don't need descriptions
2. **Intersection with HTML attributes** - Allows pass-through of `onClick`, `aria-*`, `style`, etc.
3. **Compact** - No separate `@typedef` needed for single-use prop definitions
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

## When to Use Other Styles

**Use nested property style** when props include business logic objects that benefit from descriptions:
```javascript
/**
 * @param {Object} props
 * @param {WorkRecord[]} props.workRecords - Records to display in the table
 * @param {RouteParams} props.params - Route parameters from Next.js
 * @param {string} [props.className] - Container class
 */
function WorkRecordTable({ workRecords, params, className }) { ... }
```

See [Function-Level Types](/architecture/jsdoc/types/jsdoc-function-level-types.md) for this pattern.

**Use a separate `@typedef`** when:
- The type is reused across multiple components
- The type is complex with many properties
- You need to export the type for external use

## Related Notes
- [Function-Level Types](/architecture/jsdoc/types/jsdoc-function-level-types.md) - Nested style for business logic props
- [JSDoc Basic Annotations](/architecture/jsdoc/syntax/jsdoc-basic-annotations.md)
- [JSDoc Generics](/architecture/jsdoc/syntax/jsdoc-generics.md)
- [Component Composition](/architecture/components/component-composition.md)
