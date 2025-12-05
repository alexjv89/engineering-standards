# JSDoc: Generic Types with @template

Use @template for generic type parameters that work with any type.

## Basic Generic Function

```javascript
/**
 * Fetches data from an endpoint
 * @template T
 * @param {string} url - API endpoint
 * @returns {Promise<T>} Parsed response data
 */
async function fetchData(url) {
  const response = await fetch(url);
  return response.json();
}

// Usage with type hint
/** @type {Promise<User>} */
const userPromise = fetchData('/api/users/123');
```

## Generic Type Definition

```javascript
/**
 * Generic list wrapper
 * @template T
 * @typedef {Object} List
 * @property {T[]} items - Array of items
 * @property {number} count - Item count
 */

/**
 * @template T
 * @param {T[]} items
 * @returns {List<T>}
 */
function createList(items) {
  return {
    items,
    count: items.length
  };
}
```

## Generic API Response

```javascript
/**
 * @typedef {Object} ApiResponse
 * @template T
 * @property {T} data - Response data
 * @property {string} [error] - Error message if any
 */

/**
 * @template T
 * @param {string} url
 * @returns {Promise<ApiResponse<T>>}
 */
async function apiRequest(url) {
  const response = await fetch(url);
  return {
    data: await response.json(),
    error: response.ok ? undefined : 'Request failed'
  };
}
```

## Related Notes
- [JSDoc Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
- [JSDoc Basic Annotations](/architecture/jsdoc/syntax/jsdoc-basic-annotations.md)
- [JavaScript with JSDoc Principle](/principles/javascript-with-jsdoc.md)
