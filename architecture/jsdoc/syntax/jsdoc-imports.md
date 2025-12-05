# JSDoc: Importing Types from Other Files

Import and reuse type definitions from other modules using JSDoc import syntax.

## Basic Type Import

```javascript
/**
 * @typedef {import('./user').User} User
 */

/**
 * @param {User} user
 */
function processUser(user) {
  // Can reference User type from another file
}
```

## Importing Multiple Types

```javascript
/**
 * @typedef {import('./user').User} User
 * @typedef {import('./user').UserRole} UserRole
 * @typedef {import('./api').ApiResponse} ApiResponse
 */

/**
 * @param {User} user
 * @param {UserRole} role
 * @returns {Promise<ApiResponse>}
 */
async function updateUserRole(user, role) {
  // Implementation
}
```

## Importing from Node Modules

```javascript
/**
 * @typedef {import('express').Request} Request
 * @typedef {import('express').Response} Response
 */

/**
 * @param {Request} req
 * @param {Response} res
 */
function handleRequest(req, res) {
  res.json({ status: 'ok' });
}
```

## Using Imported Types in New Definitions

```javascript
/**
 * @typedef {import('./user').User} User
 * @typedef {Object} UserWithStats
 * @property {User} user - Base user object
 * @property {number} loginCount - Number of logins
 * @property {Date} lastLogin - Last login date
 */

/**
 * @param {string} userId
 * @returns {Promise<UserWithStats>}
 */
async function getUserWithStats(userId) {
  // Implementation
}
```

## Optional Type Checking

Enable type checking in specific files with `@ts-check`:

```javascript
// @ts-check

/**
 * @typedef {import('./user').User} User
 */

/**
 * @param {string} name
 * @returns {string}
 */
function greet(name) {
  return `Hello ${name}`;
}

// Editor will warn: Argument of type 'number' not assignable to 'string'
greet(123);
```

## Related Notes
- [JSDoc Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
- [Module Organization](/architecture/modules/module-exports-pattern.md)
- [JavaScript with JSDoc Principle](/principles/javascript-with-jsdoc.md)
