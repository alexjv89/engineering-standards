# JSDoc: Module Augmentation for Library Types

Override incorrect or overly broad library types using TypeScript declaration files (`.d.ts`).

## When to Use

Use module augmentation when:
- A library's return type is wrong **everywhere** in your app
- The library type is too generic for your specific use case
- You want to fix the type once, not cast in every file

## Example: Next.js useParams

Next.js `useParams` returns `string | string[]` to handle catch-all routes. If your app never uses catch-all routes, override it:

```typescript
// src/types/next.d.ts
import 'next/navigation';

declare module 'next/navigation' {
  /**
   * Override useParams to return string values instead of ParamValue.
   * Our app doesn't use catch-all routes, so params are always strings.
   */
  export function useParams<
    T extends Record<string, string> = {
      t_id: string;
      p_id: string;
      d_id?: string;
    }
  >(): T;
}
```

## How It Works

1. **Import the original module** - `import 'next/navigation'`
2. **Declare module override** - `declare module 'next/navigation'`
3. **Re-export with new types** - Define the function with correct types

## File Organization

Place augmentation files in `src/types/`:

```
src/types/
├── next.d.ts        # Next.js overrides
├── jwt-decode.d.ts  # JWT library overrides (if needed)
└── ...
```

## Include in jsconfig.json

Ensure `.d.ts` files are included:

```json
{
  "include": ["src/**/*", "src/types/**/*.d.ts"]
}
```

## When NOT to Use

Don't use module augmentation when:
- You need different types in different places (use wrapper functions instead)
- The type is correct, just needs transformation (use wrapper functions)
- It's a one-off usage (inline cast may be acceptable)

## Related Notes
- [Function-Level Types](/architecture/jsdoc/types/jsdoc-function-level-types.md)
- [Wrapper Functions](/architecture/jsdoc/types/jsdoc-wrapper-functions.md)
- [Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
