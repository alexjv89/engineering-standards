# JSDoc: Callback Functions with @callback

Document function type parameters using @callback for higher-order functions.

## Basic Callback Definition

```javascript
/**
 * @callback FilterFunction
 * @param {*} item - Item to filter
 * @param {number} index - Item index
 * @returns {boolean} Whether to keep item
 */

/**
 * @param {Array} items
 * @param {FilterFunction} filterFn
 * @returns {Array}
 */
function filterItems(items, filterFn) {
  return items.filter(filterFn);
}
```

## Callback with Multiple Parameters

```javascript
/**
 * @callback CompareFunction
 * @param {*} a - First value
 * @param {*} b - Second value
 * @returns {number} Negative if a < b, positive if a > b, 0 if equal
 */

/**
 * @param {Array} items
 * @param {CompareFunction} compareFn
 * @returns {Array}
 */
function customSort(items, compareFn) {
  return [...items].sort(compareFn);
}
```

## Async Callback

```javascript
/**
 * @callback AsyncProcessor
 * @param {*} item - Item to process
 * @returns {Promise<*>} Processed result
 */

/**
 * @param {Array} items
 * @param {AsyncProcessor} processor
 * @returns {Promise<Array>}
 */
async function processAllAsync(items, processor) {
  return Promise.all(items.map(processor));
}
```

## Event Handler Callbacks

```javascript
/**
 * @callback EventHandler
 * @param {Event} event - DOM event
 * @returns {void}
 */

/**
 * @param {string} selector - CSS selector
 * @param {string} eventType - Event type
 * @param {EventHandler} handler - Event handler function
 */
function addListener(selector, eventType, handler) {
  document.querySelector(selector)?.addEventListener(eventType, handler);
}
```

## Related Notes
- [JSDoc Type Definitions](/architecture/jsdoc/syntax/jsdoc-typedef.md)
- [JSDoc Generic Types](/architecture/jsdoc/syntax/jsdoc-generics.md)
- [Arrow Functions vs Declarations](/architecture/arrow-vs-declaration.md)
