# JSDoc: Wrapper Functions for Typed APIs

Create thin wrapper functions to add type safety to generic library functions.

## When to Use

Use wrapper functions when:
- A library function is intentionally generic (works with many data shapes)
- You have a specific data shape for your use case
- The typed version would be called in multiple places

## Example: JWT Decode

`jwtDecode` returns a generic `JwtPayload`. Create a typed wrapper for your specific tokens:

```javascript
// @ts-check
import { jwtDecode } from 'jwt-decode';

/**
 * @typedef {Object} VimanaJwtPayload
 * @property {string} tnt - Tenant slug
 * @property {string} [sub] - Subject (user ID)
 * @property {number} [iat] - Issued at timestamp
 * @property {number} [exp] - Expiration timestamp
 */

/**
 * Decode a Vimana JWT token
 *
 * @param {string} token - The JWT token string
 * @returns {VimanaJwtPayload} The decoded payload with Vimana-specific claims
 */
export function decodeVimanaJwt(token) {
  return jwtDecode(token);
}
```

## How It Works

With `strict: false` in jsconfig.json, the `@returns` annotation acts as an implicit type assertion. No inline cast needed.

## Consumer Code Stays Clean

```javascript
import { decodeVimanaJwt } from "@/utils/auth";

const decoded = decodeVimanaJwt(token);
console.log(decoded.tnt); // TypeScript knows this is a string
```

## Wrapper vs Module Augmentation

| Approach | When to Use |
|----------|-------------|
| **Wrapper function** | Different types for different data shapes |
| **Module augmentation** | Same override for ALL calls |

**Example decision:**
- `jwtDecode` for Vimana tokens vs Auth0 tokens → Wrapper (different shapes)
- `useParams` always returns strings → Module augmentation (global fix)

## Naming Convention

Name wrappers to indicate the specific use case:
- `decodeVimanaJwt` (not just `decodeJwt`)
- `fetchPlantDevices` (not just `fetchDevices`)

## Related Notes
- [Function-Level Types](/architecture/jsdoc/types/jsdoc-function-level-types.md)
- [Module Augmentation](/architecture/jsdoc/types/jsdoc-module-augmentation.md)
- [JSDoc Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
