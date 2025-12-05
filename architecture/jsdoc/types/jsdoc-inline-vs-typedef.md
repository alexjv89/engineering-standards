# JSDoc: Inline Types vs @typedef

Prefer inline types. Use @typedef only when a type is reused 3+ times or needs documentation.

## Prefer Inline Types

When a type is used once, write it inline:

```javascript
/**
 * @param {Object} props
 * @param {User} props.user - The user object
 * @param {(User & { score: number })[]} props.rankedUsers - Users with scores
 * @returns {React.JSX.Element}
 */
function UserList({ user, rankedUsers }) {
  // Implementation
}
```

## Avoid Unnecessary Named Typedefs

Don't create a named typedef for single-use types:

```javascript
// BAD - unnecessary abstraction
/** @typedef {User & { score: number }} RankedUser */
/** @param {RankedUser[]} props.rankedUsers */

// GOOD - inline is clearer
/** @param {(User & { score: number })[]} props.rankedUsers - Users with scores */
```

## When Named Typedefs Are Appropriate

1. **Same type used 3+ times in the file**
2. **Inline type becomes unreadable** (complex nested structures)
3. **Type needs documentation** (domain concepts requiring explanation)

```javascript
/**
 * Represents a machine state transition event from the SCADA system.
 * @typedef {Object} StateTransition
 * @property {string} machineId
 * @property {'running'|'idle'|'fault'} fromState
 * @property {'running'|'idle'|'fault'} toState
 * @property {number} timestamp
 */
```

## Decision Checklist

Before creating a named typedef, ask:
- Is this type used more than twice? If no → inline it
- Is the inline version unreadable (>80 chars, nested)? If no → inline it
- Does this type need documentation for domain understanding? If no → inline it

## Related Notes
- [JSDoc Centralized Types](/architecture/jsdoc/types/jsdoc-centralized-types.md)
- [JSDoc Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
- [JSDoc Basic Annotations](/architecture/jsdoc/syntax/jsdoc-basic-annotations.md)
