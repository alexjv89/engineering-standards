# JSDoc: Type Definitions with @typedef

Define reusable types using @typedef for complex object structures.

## Basic Type Definition

```javascript
/**
 * @typedef {Object} User
 * @property {string} id - User ID
 * @property {string} name - Full name
 * @property {string} email - Email address
 * @property {'admin'|'user'|'guest'} role - User role
 */

/**
 * @param {User} user
 * @returns {boolean}
 */
function isAdmin(user) {
  return user.role === 'admin';
}
```

## Complex Object Types

```javascript
/**
 * @typedef {Object} ApiResponse
 * @property {boolean} success - Whether request succeeded
 * @property {*} data - Response data (any type)
 * @property {string} [error] - Error message (optional)
 * @property {number} statusCode - HTTP status code
 */

/**
 * @param {string} url
 * @returns {Promise<ApiResponse>}
 */
async function fetchData(url) {
  const response = await fetch(url);
  return {
    success: response.ok,
    data: await response.json(),
    statusCode: response.status
  };
}
```

## Related Notes
- [JSDoc Inline Types vs @typedef](/architecture/jsdoc/types/jsdoc-inline-vs-typedef.md)
- [JSDoc Centralized Types](/architecture/jsdoc/types/jsdoc-centralized-types.md)
- [JSDoc Basic Annotations](/architecture/jsdoc/syntax/jsdoc-basic-annotations.md)
- [JSDoc Generic Types](/architecture/jsdoc/syntax/jsdoc-generics.md)
- [JSDoc Importing Types](/architecture/jsdoc/syntax/jsdoc-imports.md)
