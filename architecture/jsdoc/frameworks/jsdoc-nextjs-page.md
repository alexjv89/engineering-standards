# JSDoc: Next.js Page Component Types

Type Next.js App Router page components with proper `searchParams` handling.

## Server-Side Page Pattern

```javascript
// @ts-check
import { getDB } from "@/database";

/**
 * @param {Object} props
 * @param {Promise<Record<string, string>>} props.searchParams
 * @returns {Promise<React.JSX.Element>}
 */
export default async function Page({ searchParams }) {
  const resolvedSearchParams = await searchParams;
  const db = getDB();

  // Use resolvedSearchParams.filter, resolvedSearchParams.page, etc.

  return (
    <div>...</div>
  );
}
```

## Key Points

1. **searchParams is a Promise** in Next.js 15+ App Router
2. **Await before using**: `const resolved = await searchParams`
3. **Return type**: `Promise<React.JSX.Element>` for async components
4. **Record<string, string>**: All URL params are strings

## With Route Params

```javascript
/**
 * @param {Object} props
 * @param {Promise<{ id: string }>} props.params
 * @param {Promise<Record<string, string>>} props.searchParams
 * @returns {Promise<React.JSX.Element>}
 */
export default async function Page({ params, searchParams }) {
  const { id } = await params;
  const resolvedSearchParams = await searchParams;
  // ...
}
```

## Client Components

Client page components don't use async:

```javascript
// @ts-check
"use client";

/**
 * @param {Object} props
 * @param {{ id: string }} props.params
 * @returns {React.JSX.Element}
 */
export default function Page({ params }) {
  return <div>{params.id}</div>;
}
```

## Related Notes
- [Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
- [JSDoc Basic Annotations](/architecture/jsdoc/syntax/jsdoc-basic-annotations.md)
