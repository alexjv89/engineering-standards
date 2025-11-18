# Service Layer Pattern

Centralize API calls in service modules using exported functions.

## Pattern

Create dedicated service modules for API interactions:

```javascript
// services/userService.js
import { apiClient } from './apiClient.js';

/**
 * @typedef {Object} User
 * @property {string} id
 * @property {string} name
 * @property {string} email
 */

/**
 * Get user by ID
 * @param {string} user_id
 * @returns {Promise<User>}
 */
export async function getUser(user_id) {
  const response = await apiClient.get(`/users/${user_id}`);
  return response.data;
}

/**
 * Update user
 * @param {string} user_id
 * @param {Partial<User>} updates
 * @returns {Promise<User>}
 */
export async function updateUser(user_id, updates) {
  const response = await apiClient.put(`/users/${user_id}`, updates);
  return response.data;
}

/**
 * Delete user
 * @param {string} user_id
 * @returns {Promise<void>}
 */
export async function deleteUser(user_id) {
  await apiClient.delete(`/users/${user_id}`);
}

/**
 * List users with optional filters
 * @param {Object} [filters={}]
 * @returns {Promise<User[]>}
 */
export async function listUsers(filters = {}) {
  const response = await apiClient.get('/users', { params: filters });
  return response.data;
}
```

## Usage in Components

```javascript
import * as userService from '@/services/userService.js';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    userService.getUser(userId).then(setUser);
  }, [userId]);

  const handleUpdate = async (updates) => {
    const updated_user = await userService.updateUser(userId, updates);
    setUser(prev => ({ ...prev, ...updates }));
  };

  return <div>{/* render user */}</div>;
}
```

## Benefits

- **Centralization**: All API calls in one place
- **Consistency**: Uniform error handling and data transformation
- **Testability**: Easy to mock service layer
- **Maintainability**: Change API structure in one place
- **Readability**: Namespace imports (`userService.getUser()`) make it clear which module functions belong to
- **Tree Shaking**: Individual function exports allow bundlers to eliminate unused code at build time

## API Client Configuration

```javascript
// services/apiClient.js
import axios from 'axios';

export const apiClient = axios.create({
  baseURL: process.env.API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add auth token to requests
apiClient.interceptors.request.use((config) => {
  const auth_token = getAuthToken();
  if (auth_token) {
    config.headers.Authorization = `Bearer ${auth_token}`;
  }
  return config;
});

// Handle auth errors
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    const status_code = error.response?.status;
    if (status_code === 401) {
      redirectToLogin();
    }
    return Promise.reject(error);
  }
);
```

## Related Notes
- [Module Organization](./module-organization.md)
- [Functional Programming](../../principles/functional-programming.md)
- [Custom Hooks for Data Fetching](../components/custom-hooks.md)
