# JSDoc: Centralized Type Definitions

Create dedicated `.types.js` files only for core domain models used across many files.

## When to Centralize

Only create shared types when ALL are true:
- **Already used in 3+ files** (not speculatively)
- **Represents a core domain model** (database record, API response)
- **Has a stable contract** (won't change frequently)

**Good candidates**: Database models (User, Device, Alert), API responses (SVCDevice)
**Bad candidates**: Component props, computed types, types used in 1-2 files

## Directory Structure

```
/src/types/
├── database/              # Database model types
│   ├── Statement.types.js
│   └── index.js          # Re-exports all database types
└── svc/                  # External API types
    └── index.js
```

## Type Definition File

```javascript
// src/types/database/Statement.types.js
/**
 * @typedef {Object} Statement
 * @property {string} id
 * @property {string} org
 * @property {string} file_name
 */
module.exports = {};
```

## Central Re-export

```javascript
// src/types/database/index.js
/**
 * @typedef {import('./Statement.types').Statement} Statement
 */
module.exports = {};
```

## Usage

```javascript
/**
 * @typedef {import('@/types/database').Statement} Statement
 * @param {Statement} props.statement
 */
```

## Anti-Patterns

```javascript
// BAD - speculative centralization
// src/types/UserWithStats.js (only used in one file)

// BAD - computed types belong where computed
// src/types/PlantWithDeviceCount.js

// BAD - props are component-specific
// src/types/UserCardProps.js
```

Three similar local types are better than a premature shared abstraction.

## Related Notes
- [JSDoc Inline Types vs @typedef](/architecture/jsdoc/types/jsdoc-inline-vs-typedef.md)
- [JSDoc Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
- [JSDoc API Type Prefixes](/architecture/jsdoc/types/jsdoc-api-type-prefixes.md)
