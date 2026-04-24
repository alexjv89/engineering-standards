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

## Nested Property Style

Use nested properties when parameters include **business logic objects** that benefit from descriptions:

```javascript
/**
 * @param {Object} options - Configuration options
 * @param {Object} options.cookieStore - Cookie store for authentication
 * @param {string} options.plant - Plant ID
 * @param {boolean} [options.withDataItems=false] - Include data items
 */
```

### When to Use This Style

| Props are... | Style | Example |
|--------------|-------|---------|
| Domain/business objects | **Nested** (this style) | `workRecords`, `pricingConfig` |
| Self-documenting UI conventions | Inline object | `className`, `variant`, `onClick` |

**Nested style** - Props need context, descriptions add value:
- `workRecords` - What kind of records? What's included?
- `routeParams` - Which parameters? From where?
- `pricingConfig` - What settings? What are defaults?

**Inline style** - Props are framework/UI conventions, self-documenting:
- `className`, `variant`, `size` - React/UI patterns everyone knows
- `onClick`, `disabled` - Standard HTML attributes

For React components with mostly UI props, see [shadcn Component Props](/architecture/jsdoc/frameworks/jsdoc-shadcn-component-props.md).

### Don't Be Lazy

Don't just type `{Object}` without nested properties:

```javascript
// BAD - no context
/**
 * @param {Object} options
 */

// GOOD - detailed
/**
 * @param {Object} options - Configuration options
 * @param {Object} options.cookieStore - Cookie store for authentication
 * @param {string} options.plant - Plant ID
 */
```

## Related Notes
- [shadcn Component Props](/architecture/jsdoc/frameworks/jsdoc-shadcn-component-props.md) - Inline style for UI components
- [Module Augmentation](/architecture/jsdoc/types/jsdoc-module-augmentation.md)
- [Wrapper Functions](/architecture/jsdoc/types/jsdoc-wrapper-functions.md)
- [Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
