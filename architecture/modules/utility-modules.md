# Utility Modules

Organize utility functions following the same pattern as UI components: one function per file with named exports.

## Pattern: One Function Per File

Each utility function lives in its own file, with the function name matching the filename:

```javascript
// src/utils/dates/formatDate.js
/**
 * Format date to ISO date string
 * @param {Date} date
 * @returns {string}
 */
export function formatDate(date) {
  return date.toISOString().split('T')[0];
}
```

```javascript
// src/utils/dates/addDays.js
/**
 * Add days to a date
 * @param {Date} date
 * @param {number} days
 * @returns {Date}
 */
export function addDays(date, days) {
  const result = new Date(date);
  result.setDate(result.getDate() + days);
  return result;
}
```

```javascript
// src/utils/dates/isWeekend.js
/**
 * Check if date is a weekend
 * @param {Date} date
 * @returns {boolean}
 */
export function isWeekend(date) {
  const day_of_week = date.getDay();
  return day_of_week === 0 || day_of_week === 6;
}
```

## File Organization

Organize functions in domain folders. Only create subfolders when a domain has >10 functions:

```
src/utils/
├── dates/              # Date utilities (9 functions - keep flat)
│   ├── formatDate.js
│   ├── addDays.js
│   ├── isWeekend.js
│   └── index.js       # Optional barrel export
├── strings/            # String operations
│   ├── capitalize.js
│   ├── truncate.js
│   ├── slugify.js
│   └── index.js
├── objects/            # Object transformations
│   ├── pick.js
│   ├── omit.js
│   └── index.js
└── validation/
    ├── isEmail.js
    └── isURL.js
```

## Usage: Individual Imports

Import functions directly from their files:

```javascript
import { formatDate } from '@/utils/dates/formatDate';
import { addDays } from '@/utils/dates/addDays';
import { capitalize } from '@/utils/strings/capitalize';

const formatted_date = formatDate(new Date());
const next_week = addDays(new Date(), 7);
const title = capitalize('hello world');
```

## Usage: Namespaced Imports

Use barrel exports (index.js) for namespace-style imports:

```javascript
// src/utils/dates/index.js
export { formatDate } from './formatDate';
export { addDays } from './addDays';
export { isWeekend } from './isWeekend';
```

```javascript
// Usage with namespace
import * as dateUtils from '@/utils/dates';

const formatted_date = dateUtils.formatDate(new Date());
const next_week = dateUtils.addDays(new Date(), 7);
const is_weekend = dateUtils.isWeekend(new Date());
```

## Example: Object Utilities

```javascript
// src/utils/objects/pick.js
/**
 * Pick specific properties from object
 * @param {Object} obj
 * @param {string[]} keys
 * @returns {Object}
 */
export function pick(obj, keys) {
  return keys.reduce((result, key) => {
    if (key in obj) {
      result[key] = obj[key];
    }
    return result;
  }, {});
}
```

```javascript
// src/utils/objects/omit.js
/**
 * Omit specific properties from object
 * @param {Object} obj
 * @param {string[]} keys
 * @returns {Object}
 */
export function omit(obj, keys) {
  return Object.keys(obj).reduce((result, key) => {
    if (!keys.includes(key)) {
      result[key] = obj[key];
    }
    return result;
  }, {});
}
```

```javascript
// Usage
import { omit } from '@/utils/objects/omit';

const user = { id: 1, name: 'Alice', password: 'secret', email: 'alice@example.com' };
const safe_user = omit(user, ['password']);
```

## Testing Pattern

Each utility function has a corresponding test file with 1:1 mapping:

```
src/utils/dates/
├── formatDate.js
├── addDays.js
└── isWeekend.js

tests/utils/dates/
├── formatDate.test.js
├── addDays.test.js
└── isWeekend.test.js
```

```javascript
// tests/utils/dates/formatDate.test.js
import { formatDate } from '@/utils/dates/formatDate';

describe('formatDate', () => {
  it('should format date to ISO string', () => {
    const date = new Date('2025-12-12T10:30:00Z');
    expect(formatDate(date)).toBe('2025-12-12');
  });

  it('should handle invalid dates', () => {
    expect(() => formatDate(null)).toThrow();
  });
});
```

## Keep Utilities Pure

Utilities should be pure functions when possible:

```javascript
// Good - pure function
export function calculateDiscount(price, percent) {
  return price * (1 - percent / 100);
}

// Avoid - side effects
let total = 0;
export function addToTotal(amount) {
  total += amount;  // Side effect
  return total;
}
```

## Benefits

✅ **Testability** - 1:1 mapping between function and test file
✅ **Discoverability** - Function location is predictable from name
✅ **Refactoring** - Change one function without affecting others
✅ **Code Review** - Smaller, focused changes
✅ **Tree-shaking** - Import only what you need
✅ **Consistency** - Same pattern as UI components
✅ **Namespace support** - Use barrel exports for grouped imports

## When to Create Subfolders

Only create subfolders when a domain has >10 functions:

```
src/utils/
├── dates/              # 9 functions - FLAT (no subfolders)
│   ├── formatDate.js
│   ├── addDays.js
│   └── ...
└── server/             # 20+ functions - USE subfolders
    ├── files/
    │   ├── readCSV.js
    │   └── readXLSX.js
    ├── database/
    │   └── buildQuery.js
    └── ...
```

## Related Notes
- [Module Exports Pattern](/architecture/modules/module-exports-pattern.md)
- [Functional Programming](/principles/functional-programming.md)
- [Pure Functions](/architecture/functional/pure-functions.md)
- [Files: Utilities](/naming/files-utilities.md)
- [Files: Components](/naming/files-components.md)
