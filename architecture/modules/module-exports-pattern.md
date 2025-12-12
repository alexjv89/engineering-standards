# Module Exports Pattern

Organize code into functional modules using named exports rather than classes.

## Pattern: Named Exports (One Function Per File)

Each function lives in its own file with a named export matching the filename:

```javascript
// src/services/users/getUser.js

/**
 * @typedef {Object} User
 * @property {string} id
 * @property {string} name
 * @property {string} email
 * @property {boolean} active
 */

/**
 * Get user by ID
 * @param {string} user_id
 * @returns {Promise<User>}
 */
export async function getUser(user_id) {
  const response = await fetch(`/api/users/${user_id}`);
  if (!response.ok) {
    throw new Error('User not found');
  }
  return response.json();
}
```

```javascript
// src/services/users/createUser.js

/**
 * Create new user
 * @param {Partial<User>} user_data
 * @returns {Promise<User>}
 */
export async function createUser(user_data) {
  const response = await fetch('/api/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(user_data)
  });
  return response.json();
}
```

```javascript
// src/services/users/listActiveUsers.js
import { listUsers } from './listUsers';

/**
 * List all active users
 * @returns {Promise<User[]>}
 */
export async function listActiveUsers() {
  const all_users = await listUsers();
  return all_users.filter(user => user.active);
}
```

```javascript
// src/services/users/listUsers.js

/**
 * List all users (internal helper)
 * @returns {Promise<User[]>}
 */
export async function listUsers() {
  const response = await fetch('/api/users');
  return response.json();
}
```

## Usage: Individual Imports

Import functions directly from their files:

```javascript
import { getUser } from '@/services/users/getUser';
import { updateUser } from '@/services/users/updateUser';

async function updateUserProfile(user_id, new_name) {
  const user = await getUser(user_id);
  return await updateUser(user_id, { ...user, name: new_name });
}
```

## Usage: Namespace Imports

Use barrel exports for namespace-style imports:

```javascript
// src/services/users/index.js
export { getUser } from './getUser';
export { createUser } from './createUser';
export { updateUser } from './updateUser';
export { listActiveUsers } from './listActiveUsers';
```

```javascript
// Usage with namespace
import * as userService from '@/services/users';

const user = await userService.getUser('123');
await userService.updateUser('123', { name: 'New Name' });
```

## Benefits

✅ **Simple** - Just functions, no `this` binding
✅ **Testable** - Each function can be tested independently (1:1 test mapping)
✅ **Tree-shakeable** - Import only what you use
✅ **Clear dependencies** - Function parameters show what's needed
✅ **No state** - Functions don't carry hidden state
✅ **Consistent** - Same pattern as UI components
✅ **Discoverable** - Function location matches function name
✅ **Refactorable** - Change one function without affecting others

## Named Exports Only

Always use named exports that match the filename:

```javascript
// Good - named export matches filename
// src/services/users/getUser.js
export function getUser(user_id) { }

// Avoid - default export
export default function getUser(user_id) { }

// Avoid - anonymous default export
export default function(user_id) { }
```

**Why named exports?**
- Better IDE autocomplete and refactoring support
- Clear function name in imports
- Consistent with component pattern
- Easier to grep/search codebase
- No ambiguity about function names

## Related Notes
- [Service Layer Pattern](/architecture/modules/service-layer-pattern.md)
- [Utility Modules](/architecture/modules/utility-modules.md)
- [Module Shared State](/architecture/modules/module-shared-state.md)
- [Functional Programming](/principles/functional-programming.md)
