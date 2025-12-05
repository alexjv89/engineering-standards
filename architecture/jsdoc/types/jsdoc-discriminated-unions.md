# JSDoc: Discriminated Union Types

Use union types with literal string discriminators when an array or object can hold multiple different shapes.

## The Problem

TypeScript infers array element types from the first element:

```javascript
// @ts-check

// TypeScript infers: values must be { gte: Date, lt: Date }
const filters = [
  {
    filterType: "range",
    fieldName: "timestamp",
    values: { gte: startDate, lt: endDate }
  }
];

// ERROR: Type 'string[]' not assignable to '{ gte: Date, lt: Date }'
filters.push({
  filterType: "terms",
  fieldName: "device",
  values: ["device-1", "device-2"]  // Different shape!
});
```

## The Solution: Discriminated Unions

Define separate types with literal `filterType` values, then combine with union:

```javascript
// @ts-check

/**
 * @typedef {Object} RangeFilter
 * @property {'range'} filterType
 * @property {string} fieldName
 * @property {{ gte: Date, lt: Date }} values
 */

/**
 * @typedef {Object} TermsFilter
 * @property {'terms'} filterType
 * @property {string} fieldName
 * @property {string[]} values
 */

/** @typedef {RangeFilter | TermsFilter} Filter */

// Now annotate the array
/** @type {Filter[]} */
const filters = [
  {
    filterType: "range",
    fieldName: "timestamp",
    values: { gte: startDate, lt: endDate }
  }
];

// Works! TypeScript knows filters can hold either shape
filters.push({
  filterType: "terms",
  fieldName: "device",
  values: ["device-1", "device-2"]
});
```

## Key Pattern

1. **Define each variant** with a literal string discriminator (`'range'`, `'terms'`)
2. **Combine with union** using `|` operator
3. **Annotate the container** with `@type`

## Success/Error Pattern with Proper Narrowing

For functions returning success or error, use the `undefined` trick to enable proper type narrowing:

```javascript
/**
 * @typedef {Object} FetchDataSuccess
 * @property {true} success
 * @property {DataType} data
 * @property {undefined} [error]  // Key for proper narrowing!
 */

/**
 * @typedef {Object} FetchDataError
 * @property {false} success
 * @property {string} error
 */

/** @typedef {FetchDataSuccess | FetchDataError} FetchDataResult */
```

The `@property {undefined} [error]` on the success type tells TypeScript that `error` doesn't exist on success objects, enabling proper narrowing:

```javascript
const result = await fetchData();

if (result.success) {
  // TypeScript knows: result.data exists, result.error is undefined
  console.log(result.data);
} else {
  // TypeScript knows: result.error exists
  console.error(result.error);
}
```

Without the `undefined` property, TypeScript may not narrow correctly in the else branch.

## When to Use

- Arrays that hold objects of different shapes
- API request/response bodies with multiple formats
- Event objects with different payloads
- Filter systems with various filter types
- Server action results (success/error)

## Related Notes
- [JSDoc Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
- [JSDoc Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
- [JSDoc Basic Annotations](/architecture/jsdoc/syntax/jsdoc-basic-annotations.md)
- [Server Action Results](/architecture/jsdoc/types/jsdoc-server-action-results.md)
