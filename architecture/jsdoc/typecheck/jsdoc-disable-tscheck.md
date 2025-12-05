# JSDoc: When to Disable @ts-check

Some files legitimately cannot use `@ts-check` due to third-party library type issues or complex patterns.

## Valid Reasons to Disable

### Third-Party Library Type Mismatches

When a library's types don't match actual behavior:

```javascript
// Note: @ts-check disabled due to AI SDK v5 type mismatches with useChat and DefaultChatTransport
"use client";
import { useChat } from "@ai-sdk/react";
```

### Complex React Patterns

Patterns like `asChild` with `React.cloneElement` create type complexity:

```javascript
// Note: @ts-check disabled due to React.cloneElement type complexity with asChild pattern
import * as React from "react"
import { Slot } from "@radix-ui/react-slot"
```

### Page Components with async.auto

Next.js page components using `async.auto` return `any` for workflow results. Typing them would require inline casts throughout the function body:

```javascript
// Don't add @ts-check - async.auto returns any
import async from "async";

export default async function Page({ searchParams }) {
  const workflow = {
    getUsers: async function () { /* ... */ },
    getPlants: async function () { /* ... */ },
  };

  const results = await async.auto(workflow);
  // results.getUsers and results.getPlants are 'any'
  // Would need inline casts to type them, violating function-level typing

  return <Component users={results.getUsers} plants={results.getPlants} />;
}
```

For these files, only add function-level JSDoc for documentation, not `@ts-check`.

## How to Document

Always explain WHY at the top of the file:

```javascript
// Note: @ts-check disabled due to [specific reason]
```

This helps future developers understand it's intentional, not forgotten.

## Patterns That Work

These typically work with `@ts-check`:

| File Type | Usually Works |
|-----------|---------------|
| Server actions | Yes |
| Utility functions | Yes |
| Server-side page.jsx (simple) | Yes |
| Server-side page.jsx with async.auto | No |
| Simple client components | Yes |
| shadcn components with asChild | Sometimes no |
| Components using complex SDK hooks | Sometimes no |

## Prefer Partial Typing

If only one function has issues, use `@ts-ignore` on that line instead of disabling for the whole file:

```javascript
// @ts-check

// @ts-ignore - SDK types incorrect for this overload
const result = sdkMethod(complexArg);
```

## Related Notes
- [Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
- [shadcn Component Props](/architecture/jsdoc/frameworks/jsdoc-shadcn-component-props.md)
- [Function-Level Types](/architecture/jsdoc/types/jsdoc-function-level-types.md)
