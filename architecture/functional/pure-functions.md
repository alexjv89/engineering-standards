# Pure Functions

Write pure functions that always return the same output for the same input with no side effects.

## What is a Pure Function?

A pure function:
1. Returns the same result given the same arguments
2. Has no side effects (doesn't modify external state)
3. Doesn't depend on external mutable state

## Pure Function Example

```javascript
// Pure function - good
function calculateTotal(items, tax_rate) {
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  return subtotal * (1 + tax_rate);
}

// Always returns same result for same inputs
console.log(calculateTotal([{price: 100}], 0.08)); // 108
console.log(calculateTotal([{price: 100}], 0.08)); // 108
```

## Impure Function Examples

```javascript
// Impure - modifies external state
let total = 0;
function addToTotal(amount) {
  total += amount;  // Side effect
  return total;
}

// Impure - depends on external mutable state
let discount = 0.1;
function applyDiscount(price) {
  return price * (1 - discount);  // Depends on external variable
}

// Impure - modifies input argument
function addItem(cart, item) {
  cart.push(item);  // Mutates cart
  return cart;
}
```

## Making Functions Pure

```javascript
// Make it pure - no external state
function addToTotal(current_total, amount) {
  return current_total + amount;
}

// Make it pure - pass discount as parameter
function applyDiscount(price, discount_rate) {
  return price * (1 - discount_rate);
}

// Make it pure - return new array
function addItem(cart, item) {
  return [...cart, item];
}
```

## Benefits of Pure Functions

- **Testable** - Easy to test, no setup needed
- **Predictable** - Same inputs = same outputs
- **Cacheable** - Results can be memoized
- **Parallelizable** - Safe to run in parallel
- **Debuggable** - Easy to understand and debug

## Testing Pure vs Impure

```javascript
// Pure function - easy to test
test('calculateTotal adds tax correctly', () => {
  const items = [{ price: 100 }, { price: 50 }];
  expect(calculateTotal(items, 0.08)).toBe(162);
});

// Impure function - harder to test
let total = 0;
test('addToTotal increases total', () => {
  total = 0;  // Need to reset state
  addToTotal(10);
  expect(total).toBe(10);  // Testing side effect
});
```

## When Side Effects Are Necessary

Some functions must have side effects (API calls, DOM updates, logging). Keep them separate from pure logic:

```javascript
// Pure logic
function calculateOrderTotal(items, tax_rate, shipping) {
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  const tax = subtotal * tax_rate;
  return subtotal + tax + shipping;
}

// Side effect wrapper
async function submitOrder(items, tax_rate, shipping) {
  const total = calculateOrderTotal(items, tax_rate, shipping);

  // Side effect - API call
  const response = await fetch('/api/orders', {
    method: 'POST',
    body: JSON.stringify({ items, total })
  });

  return response.json();
}
```

## Related Notes
- [Function Composition](/architecture/functional/function-composition.md)
- [Immutability Patterns](/architecture/functional/immutability-patterns.md)
- [Functional Programming](/principles/functional-programming.md)
- [AAA Pattern](/testing/aaa-pattern.md)
