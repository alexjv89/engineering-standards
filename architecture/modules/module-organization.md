# Module Organization Overview

Organize code into functional modules using exported functions rather than classes.

## Philosophy

For small teams, functional modules provide:
- **Simplicity** - Functions without `this` binding
- **Testability** - Each function independently testable
- **Tree-shaking** - Import only what you need
- **Clear dependencies** - Parameters show what's needed

See [Functional Programming Principle](/principles/functional-programming.md) for the underlying philosophy.

## Module Organization Topics

### [Module Exports Pattern](/architecture/modules/module-exports-pattern.md)
How to structure and export functions from modules:
- Named exports vs default exports
- Named imports vs namespace imports
- Private helper functions
- Module structure best practices

### [Module Shared State](/architecture/modules/module-shared-state.md)
When modules need shared configuration:
- Module-level variables with closure pattern
- Configure function pattern
- When to use vs when to avoid
- Alternative: dependency injection

### [Utility Modules](/architecture/modules/utility-modules.md)
Organizing utility functions:
- Grouping related utilities
- File organization patterns
- Pure utility functions
- Object transformation helpers

### [Where a Piece of Code Lives](/architecture/modules/where-code-lives.md)
Deciding where code goes:
- Inline, named function, adjacent file or central folder
- What counts as a caller

## Quick Example

```javascript
// userService.js - Functional module

/**
 * @typedef {Object} User
 * @property {string} id
 * @property {string} name
 * @property {string} email
 */

/**
 * @param {string} user_id
 * @returns {Promise<User>}
 */
export async function getUser(user_id) {
  const response = await fetch(`/api/users/${user_id}`);
  return response.json();
}

/**
 * @param {Partial<User>} user_data
 * @returns {Promise<User>}
 */
export async function createUser(user_data) {
  const response = await fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify(user_data)
  });
  return response.json();
}

// Usage
import { getUser, createUser } from './userService.js';

const user = await getUser('123');
const new_user = await createUser({ name: 'Alice' });
```

## Don't Use Classes

Prefer functional modules over classes for most code:

```javascript
// ❌ Avoid - Class-based
class UserService {
  constructor(api_client) {
    this.apiClient = api_client;
  }

  async getUser(user_id) {
    return this.apiClient.get(`/users/${user_id}`);
  }
}

// ✅ Prefer - Functional module
export async function getUser(api_client, user_id) {
  return api_client.get(`/users/${user_id}`);
}
```

**Exception**: React Error Boundaries are the only place classes are required in React.

## Related Notes
- [Functional Programming Principle](/principles/functional-programming.md)
- [Service Layer Pattern](/architecture/modules/service-layer-pattern.md)
- [Functions: camelCase](/naming/functions-camelcase.md)
- [Error Boundaries](/architecture/error-boundaries.md)
