# JSDoc: Type Prefixes for External APIs

Prefix type names to indicate their source when defining types for external API responses.

## Why Prefix?

- Distinguishes external API types from internal database models
- Avoids naming collisions (e.g., `Device` from API vs `Device` from database)
- Makes imports self-documenting
- Clear provenance of data structures

## Naming Convention

```
{Source}{EntityName}
```

| Source | Prefix | Example |
|--------|--------|---------|
| Service/Backend API | `SVC` | `SVCDevice`, `SVCAlert` |
| Database models | None (use as-is) | `Device`, `Alert` |
| Third-party API | API name | `StripeCustomer`, `GitHubRepo` |

## Directory Structure

```
src/types/
├── database/           # Internal database model types
│   ├── Device.types.js
│   └── index.js
│
├── svc/                # External service API types
│   ├── SVCDevice.js
│   ├── SVCAlert.js
│   └── index.js
│
└── stripe/             # Third-party API types
    ├── StripeCustomer.js
    └── index.js
```

## Example: SVC API Types

```javascript
// src/types/svc/SVCDevice.js
/**
 * Device entity from the Vimana SVC API
 * @typedef {Object} SVCDevice
 * @property {string} uuid - Device UUID
 * @property {string} name - Device display name
 * @property {boolean} enabled - Whether device is enabled
 * @property {string[]} tags - Device tags
 */

module.exports = {};
```

```javascript
// src/types/svc/index.js
/**
 * @typedef {import('./SVCDevice').SVCDevice} SVCDevice
 * @typedef {import('./SVCAlert').SVCAlert} SVCAlert
 */

module.exports = {};
```

## Usage

```javascript
// @ts-check

/**
 * @typedef {import('@/types/svc').SVCDevice} SVCDevice
 * @typedef {import('@/types/database').Device} Device
 */

// Clear which is API response vs database model
/** @param {SVCDevice} apiDevice */
function processApiResponse(apiDevice) { }

/** @param {Device} dbDevice */
function processDbRecord(dbDevice) { }
```

## When to Use Prefixes

- External REST API responses
- Third-party service integrations
- Any type that represents external data structures
- When you have both internal and external versions of similar entities

## Related Notes
- [JSDoc Centralized Types](/architecture/jsdoc/types/jsdoc-centralized-types.md)
- [JSDoc Importing Types](/architecture/jsdoc/syntax/jsdoc-imports.md)
- [Module Organization](/architecture/modules/module-organization.md)
