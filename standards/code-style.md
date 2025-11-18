# Code Style Guide

This document defines the coding style and naming conventions used across all projects.

## Table of Contents
- [Naming Conventions](#naming-conventions)
- [File Organization](#file-organization)
- [Code Structure](#code-structure)
- [Comments & Documentation](#comments--documentation)
- [Imports & Exports](#imports--exports)

## Naming Conventions

### Variables & Functions

**JavaScript/TypeScript:**
- **Variables**: `camelCase` for variables and constants
  ```javascript
  const userName = "Alex";
  const maxRetryCount = 3;
  let isLoading = false;
  ```

- **Functions**: `camelCase` for function names, use verb prefixes
  ```javascript
  function fetchUserData() { }
  function calculateTotalPrice() { }
  async function getUserById(id) { }
  ```

- **Boolean variables**: Prefix with `is`, `has`, `should`, `can`
  ```javascript
  const isActive = true;
  const hasPermission = false;
  const shouldRetry = true;
  const canEdit = user.role === 'admin';
  ```

- **Constants**: `SCREAMING_SNAKE_CASE` for true constants
  ```javascript
  const API_BASE_URL = "https://api.example.com";
  const MAX_UPLOAD_SIZE = 5242880; // 5MB
  ```

**React Components:**
- **Component names**: `PascalCase`
  ```javascript
  function UserProfile() { }
  const ChatMessage = () => { };
  ```

- **Component files**: `PascalCase.jsx` or `PascalCase.tsx`
  ```
  UserProfile.jsx
  ChatMessage.tsx
  ```

- **Hooks**: Prefix with `use` in `camelCase`
  ```javascript
  function useAuth() { }
  function useFetchData(url) { }
  ```

### Files & Directories

- **Components**: `PascalCase.jsx` or `PascalCase.tsx`
  ```
  Button.jsx
  UserCard.tsx
  ChatInterface.jsx
  ```

- **Utilities/Helpers**: `camelCase.js` or `kebab-case.js`
  ```
  fetchAPI.js
  date-formatter.js
  string-utils.js
  ```

- **Test files**: Same name as source with `.test.js` or `.spec.js`
  ```
  Button.test.jsx
  fetchAPI.test.js
  UserCard.spec.tsx
  ```

- **Directories**: `kebab-case` or `camelCase` (be consistent within project)
  ```
  src/components/
  src/utils/
  src/api-clients/
  ```

### Classes & Types

- **Classes**: `PascalCase`
  ```javascript
  class UserService { }
  class DatabaseConnection { }
  ```

- **Interfaces** (TypeScript): `PascalCase`, optionally prefix with `I`
  ```typescript
  interface User { }
  interface IApiResponse { }
  ```

- **Type aliases**: `PascalCase`
  ```typescript
  type UserId = string;
  type ApiResponse<T> = { data: T; error?: string };
  ```

- **Enums**: `PascalCase` for enum name, `SCREAMING_SNAKE_CASE` for values
  ```typescript
  enum UserRole {
    ADMIN = 'admin',
    USER = 'user',
    GUEST = 'guest'
  }
  ```

## File Organization

### Directory Structure

**Component Directory Pattern:**
```
ComponentName/
├── ComponentName.jsx          # Main component
├── ComponentName.test.jsx     # Tests
├── ComponentName.stories.jsx  # Storybook stories
├── index.js                   # Re-export
└── styles.module.css          # Component-specific styles (if needed)
```

**Feature-Based Organization:**
```
src/
├── features/
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── utils/
│   │   └── index.js
│   └── dashboard/
├── components/               # Shared components
├── utils/                    # Shared utilities
└── api/                      # API clients
```

### Import Order

Organize imports in this order:
1. External dependencies (React, libraries)
2. Internal absolute imports (from `src/`)
3. Relative imports (from `./` or `../`)
4. Style imports

```javascript
// 1. External dependencies
import React, { useState, useEffect } from 'react';
import { format } from 'date-fns';

// 2. Internal absolute imports
import { fetchUserData } from '@/utils/api';
import { Button } from '@/components/ui';

// 3. Relative imports
import { UserCard } from './UserCard';
import { useAuth } from '../hooks/useAuth';

// 4. Style imports
import './styles.css';
```

## Code Structure

### Function Length

- **Ideal**: 10-20 lines
- **Maximum**: 50 lines
- If longer, break into smaller functions with descriptive names

### Indentation & Spacing

- **Indentation**: 2 spaces (not tabs)
- **Line length**: Aim for 80-100 characters, max 120
- **Blank lines**: Use to separate logical blocks

### Arrow Functions vs Function Declarations

- **Prefer arrow functions** for callbacks and simple functions
  ```javascript
  const users = data.map(user => user.name);
  const add = (a, b) => a + b;
  ```

- **Use function declarations** for top-level named functions
  ```javascript
  function processUserData(data) {
    // Complex logic
  }
  ```

### Destructuring

**Prefer destructuring** when accessing multiple properties:
```javascript
// Good
const { name, email, role } = user;

// Avoid (if using multiple times)
const name = user.name;
const email = user.email;
const role = user.role;
```

### Async/Await vs Promises

**Prefer async/await** over promise chains:
```javascript
// Good
async function fetchUserData(id) {
  try {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Failed to fetch user:', error);
    throw error;
  }
}

// Avoid
function fetchUserData(id) {
  return fetch(`/api/users/${id}`)
    .then(response => response.json())
    .catch(error => console.error(error));
}
```

## Comments & Documentation

### When to Comment

**Do comment:**
- Complex algorithms or business logic
- Non-obvious workarounds or edge cases
- Public APIs and exported functions

**Don't comment:**
- Obvious code (let code be self-documenting)
- Outdated information

### Comment Style

**Single-line comments:**
```javascript
// Calculate total price including tax
const totalPrice = basePrice * (1 + taxRate);
```

**Multi-line comments:**
```javascript
/**
 * Calculates the total price for an order including tax and shipping.
 *
 * @param {number} basePrice - The base price before tax
 * @param {number} taxRate - Tax rate as decimal (e.g., 0.08 for 8%)
 * @param {number} shippingCost - Shipping cost
 * @returns {number} Total price
 */
function calculateOrderTotal(basePrice, taxRate, shippingCost) {
  return basePrice * (1 + taxRate) + shippingCost;
}
```

### JSDoc for Functions

Use JSDoc for exported functions and APIs:
```javascript
/**
 * Fetches user data from the API
 *
 * @param {string} userId - The user's unique identifier
 * @param {object} options - Optional configuration
 * @param {boolean} options.includeProfile - Whether to include profile data
 * @returns {Promise<User>} The user object
 * @throws {Error} If user not found or network error
 */
export async function getUserById(userId, options = {}) {
  // Implementation
}
```

## Imports & Exports

### Named Exports vs Default Exports

**Prefer named exports** for better refactoring and autocomplete:
```javascript
// Good
export function calculateTotal(items) { }
export const TAX_RATE = 0.08;

// Usage
import { calculateTotal, TAX_RATE } from './utils';
```

**Use default exports** only for:
- React components (one component per file)
- Main class in a file

```javascript
// Button.jsx
export default function Button({ children }) {
  return <button>{children}</button>;
}
```

### Barrel Exports

Use `index.js` to re-export related modules:
```javascript
// components/index.js
export { Button } from './Button';
export { Input } from './Input';
export { Card } from './Card';

// Usage elsewhere
import { Button, Input, Card } from '@/components';
```

## Language-Specific Guidelines

### JavaScript/TypeScript

- Use `const` by default, `let` when reassignment is needed, avoid `var`
- Use template literals for string concatenation
  ```javascript
  const greeting = `Hello, ${userName}!`;
  ```
- Use optional chaining and nullish coalescing
  ```javascript
  const userName = user?.profile?.name ?? 'Guest';
  ```

### React

- Use functional components with hooks (not class components)
- Keep components small and focused (single responsibility)
- Extract custom hooks for reusable logic
- Use prop destructuring in component signature
  ```javascript
  function UserCard({ name, email, avatarUrl }) {
    return <div>...</div>;
  }
  ```

## Enforcement

These standards are enforced through:
- ESLint configuration
- Prettier formatting
- Code review checklists
- Claude Code automation

---

**See Also:**
- [Testing Practices](testing-practices.md)
- [Architecture Patterns](architecture-patterns.md)
