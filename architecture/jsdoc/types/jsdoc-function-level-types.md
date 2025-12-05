# JSDoc: Function-Level Types Only

Types belong in JSDoc at function boundaries (`@param`, `@returns`), NOT inline in function bodies.

## Philosophy

- **Function signatures are the contract** - Type the inputs and outputs
- **Function bodies are implementation** - Let inference handle internals
- **No inline casts** - Avoid `/** @type {X} */ (value)` inside functions

## Good: Types at Function Boundary

```javascript
/**
 * Get devices for a plant
 *
 * @param {Object} options - Configuration options
 * @param {Object} options.cookieStore - Cookie store for authentication
 * @param {string} options.plant - Plant ID
 * @returns {Promise<SVCDevice[]>} Array of device objects
 */
export async function getDevices({ cookieStore, plant }) {
  // No type annotations needed inside the function
  const response = await fetchSVC({ cookieStore, plant, url: '/api/devices' });
  return response;
}
```

## Bad: Inline Type Casts

```javascript
export async function getDevices({ cookieStore, plant }) {
  // BAD - inline type cast
  const response = /** @type {SVCDevice[]} */ (await fetchSVC({ ... }));
  return response;
}
```

## Why Avoid Inline Casts?

1. **Clutters code** - Types mixed with logic reduce readability
2. **Maintenance burden** - Must update casts when types change
3. **Masks issues** - Casts hide type mismatches instead of fixing them
4. **Not needed with `strict: false`** - `@returns` acts as implicit cast

## When External Types Don't Match

If a library returns the wrong type, don't cast inline. Instead:

| Scenario | Solution |
|----------|----------|
| Library type wrong globally | Module augmentation (`.d.ts`) |
| Need typed version of generic API | Wrapper function |

See [Module Augmentation](/architecture/jsdoc/types/jsdoc-module-augmentation.md) and [Wrapper Functions](/architecture/jsdoc/types/jsdoc-wrapper-functions.md).

## Detailed Object Parameters

Don't just type `{Object}` - document nested properties:

```javascript
// BAD - too lazy
/**
 * @param {Object} options
 */

// GOOD - detailed
/**
 * @param {Object} options - Configuration options
 * @param {Object} options.cookieStore - Cookie store for authentication
 * @param {string} options.plant - Plant ID
 * @param {boolean} [options.withDataItems=false] - Include data items
 */
```

## Related Notes
- [Module Augmentation](/architecture/jsdoc/types/jsdoc-module-augmentation.md)
- [Wrapper Functions](/architecture/jsdoc/types/jsdoc-wrapper-functions.md)
- [Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
