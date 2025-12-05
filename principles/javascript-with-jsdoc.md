# JavaScript with JSDoc (No TypeScript)

Use plain JavaScript with JSDoc comments for type documentation instead of TypeScript.

## Philosophy

As a small team, minimize complexity and moving parts:
- Fewer build steps
- Simpler tooling
- Faster iteration
- Less configuration overhead

TypeScript adds compilation steps, configuration complexity, and tooling dependencies that slow down a small team.

## Pattern: JSDoc for Type Documentation

Use JSDoc comments to document types without compilation:

```javascript
/**
 * Fetches user data from the API
 * @param {string} userId - The user's unique identifier
 * @param {Object} options - Optional configuration
 * @param {boolean} [options.includeProfile=false] - Whether to include profile
 * @returns {Promise<User>} The user object
 * @throws {Error} If user not found or network error
 */
export async function getUserById(userId, options = {}) {
  const response = await fetch(`/api/users/${userId}`);
  if (!response.ok) {
    throw new Error('User not found');
  }
  return response.json();
}
```

## Type Definitions with JSDoc

Define complex types using JSDoc typedef:

```javascript
/**
 * @typedef {Object} User
 * @property {string} id - User ID
 * @property {string} name - User's full name
 * @property {string} email - User's email address
 * @property {'admin'|'user'|'guest'} role - User role
 * @property {Date} createdAt - Account creation date
 */

/**
 * @typedef {Object} ApiResponse
 * @template T
 * @property {T} data - Response data
 * @property {string} [error] - Error message if any
 */

/**
 * Creates a new user
 * @param {Partial<User>} userData - User data
 * @returns {Promise<User>} Created user
 */
async function createUser(userData) {
  // Implementation
}
```

## Generic Types

Use JSDoc template syntax for generics:

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

## Object Shape Documentation

Document object parameters inline:

```javascript
/**
 * @param {{
 *   name: string,
 *   email: string,
 *   age?: number,
 *   roles: string[]
 * }} userData
 */
function validateUser(userData) {
  // Validation logic
}
```

## Benefits Over TypeScript

✅ **No build step** - Run JavaScript directly
✅ **Simpler tooling** - No tsconfig.json, no type errors blocking builds
✅ **Faster iteration** - No compilation time
✅ **Same IDE support** - VSCode/IDEs still provide autocomplete and type checking
✅ **Gradual adoption** - Add JSDoc where helpful, skip where not

## When JSDoc is Sufficient

For small teams, JSDoc provides:
- Type hints for IDE autocomplete
- Documentation for function signatures
- Basic type safety warnings in editors
- No runtime overhead

## File Extensions

Use standard JavaScript extensions:
- `.js` - JavaScript files
- `.jsx` - React components with JSX

**Not**: `.ts`, `.tsx`

## Editor Support

Modern editors (VSCode, WebStorm) provide:
- Autocomplete from JSDoc types
- Type checking warnings (optional)
- Hover documentation
- Go to definition

Enable in VSCode with `// @ts-check` at top of file for type checking:

```javascript
// @ts-check

/**
 * @param {string} name
 * @returns {string}
 */
function greet(name) {
  return `Hello ${name}`;
}

greet(123); // Editor warning: Argument of type 'number' not assignable to 'string'
```

## Related Notes
- [JSDoc Type Patterns](/architecture/jsdoc/syntax/jsdoc-types.md)
- [Function Documentation](/naming/functions-camelcase.md)
- [Atomic Knowledge Base Principles](/principles/atomic-knowledge-base.md)
