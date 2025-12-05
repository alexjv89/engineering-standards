# JSDoc Type Patterns Overview

Document types and function signatures using JSDoc comments in JavaScript.

## Why JSDoc?

For small teams using plain JavaScript, JSDoc provides:
- Type hints for IDE autocomplete
- Documentation for function signatures
- Optional type checking in editors
- No compilation overhead

See [JavaScript with JSDoc Principle](/principles/javascript-with-jsdoc.md) for philosophy.

## JSDoc Topics

### [Basic Annotations](/architecture/jsdoc/syntax/jsdoc-basic-annotations.md)
Document function parameters, returns, and exceptions:
- @param for parameters
- @returns for return values
- @throws for exceptions
- Optional parameters and union types

### [Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
Define reusable complex types:
- @typedef for custom types
- Object property documentation
- Inline object types

### [Generic Types](/architecture/jsdoc/syntax/jsdoc-generics.md)
Work with generic type parameters:
- @template for generics
- Generic functions and types
- Type-safe collections

### [Callback Functions](/architecture/jsdoc/syntax/jsdoc-callbacks.md)
Document function type parameters:
- @callback for function types
- Higher-order functions
- Event handlers

### [Importing Types](/architecture/jsdoc/syntax/jsdoc-imports.md)
Reuse types from other files:
- Type imports with @typedef
- Importing from node modules
- Optional type checking with @ts-check

### [Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
Configure jsconfig.json for opt-in type checking:
- `checkJs: false` with `@ts-check` per file
- NPM typecheck scripts
- Adoption strategy

### [Discriminated Unions](/architecture/jsdoc/types/jsdoc-discriminated-unions.md)
Handle arrays with multiple object shapes:
- Literal string discriminators
- Union types with `|`
- Type annotations for containers

### [API Type Prefixes](/architecture/jsdoc/types/jsdoc-api-type-prefixes.md)
Naming convention for external API types:
- `SVC` prefix for service API types
- Distinguishing API vs database types
- Directory organization

## Quick Example

```javascript
/**
 * @typedef {Object} User
 * @property {string} id
 * @property {string} name
 */

/**
 * @param {string} userId
 * @returns {Promise<User>}
 */
async function getUser(userId) {
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
}
```

## Related Notes
- [JavaScript with JSDoc Principle](/principles/javascript-with-jsdoc.md)
- [Functions: camelCase](/naming/functions-camelcase.md)
- [Module Organization](/architecture/modules/module-exports-pattern.md)
