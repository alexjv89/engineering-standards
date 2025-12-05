# JSDoc: Server Action Result Types

Use discriminated unions for server action return types to enable type-safe success/error handling.

## Pattern

Define separate success and error types, then union them:

```javascript
// @ts-check
"use server";

/**
 * @typedef {Object} SidebarData
 * @property {Object} user
 * @property {Plant[]} plants
 * @property {Tenant | null} tenant
 */

/**
 * @typedef {Object} SidebarDataSuccess
 * @property {true} success
 * @property {SidebarData} data
 * @property {undefined} [error]
 */

/**
 * @typedef {Object} SidebarDataError
 * @property {false} success
 * @property {string} error
 */

/** @typedef {SidebarDataSuccess | SidebarDataError} SidebarDataResult */

/**
 * @param {Object} params
 * @param {string} params.p_id
 * @param {string} params.t_id
 * @returns {Promise<SidebarDataResult>}
 */
export async function fetchSidebarData({ p_id, t_id }) {
  try {
    // ... fetch logic
    return {
      success: true,
      data: { user, plants, tenant }
    };
  } catch (error) {
    return {
      success: false,
      error: error.message
    };
  }
}
```

## Why This Pattern

1. **Type narrowing**: Checking `success` narrows the type
2. **No exceptions**: Caller handles both cases explicitly
3. **Consistent API**: All server actions follow same pattern
4. **Self-documenting**: Return type shows possible outcomes

## Client Usage

```javascript
const result = await fetchSidebarData({ p_id, t_id });

if (result.success) {
  // TypeScript knows: result.data exists
  console.log(result.data.user);
} else {
  // TypeScript knows: result.error exists
  console.error(result.error);
}
```

## Related Notes
- [JSDoc Discriminated Unions](/architecture/jsdoc/types/jsdoc-discriminated-unions.md)
- [Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
