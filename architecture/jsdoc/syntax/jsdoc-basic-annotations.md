# JSDoc: Basic Annotations

Document function parameters, returns, and exceptions using basic JSDoc annotations.

## Core Annotations

```javascript
/**
 * @param {string} name - User's name
 * @param {number} age - User's age
 * @param {boolean} isActive - Whether user is active
 * @returns {Object} User object
 */
function createUser(name, age, isActive) {
  return { name, age, isActive };
}
```

## Optional Parameters

Use square brackets for optional parameters:

```javascript
/**
 * @param {string} message - Message to display
 * @param {string} [level='info'] - Log level (optional, defaults to 'info')
 * @returns {void}
 */
function logMessage(message, level = 'info') {
  console.log(`[${level}] ${message}`);
}
```

## Throwing Errors

Document exceptions with @throws:

```javascript
/**
 * Fetches user data from the API
 * @param {string} userId - The user's unique identifier
 * @returns {Promise<User>} The user object
 * @throws {Error} If user not found or network error
 */
export async function getUserById(userId) {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) {
    throw new Error('User not found');
  }
  return response.json();
}
```

## Array and Union Types

```javascript
/**
 * @param {string[]} tags - Array of tag strings
 * @param {string|number} id - Can be string or number
 * @param {Array<User>} users - Array of User objects
 * @returns {void}
 */
function processTags(tags, id, users) {
  // Implementation
}
```

## Related Notes
- [JSDoc Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
- [JSDoc Generic Types](/architecture/jsdoc/syntax/jsdoc-generics.md)
- [JavaScript with JSDoc Principle](/principles/javascript-with-jsdoc.md)
