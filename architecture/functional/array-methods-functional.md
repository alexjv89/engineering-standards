# Array Methods: Functional Approach

Use built-in functional array methods for declarative data transformations.

## Core Array Methods

### map - Transform Each Item

```javascript
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' }
];

// Extract names
const names = users.map(user => user.name);
// ['Alice', 'Bob']

// Transform objects
const with_status = users.map(user => ({
  ...user,
  status: 'active'
}));
```

### filter - Select Items

```javascript
const numbers = [1, 2, 3, 4, 5, 6];

// Keep even numbers
const evens = numbers.filter(n => n % 2 === 0);
// [2, 4, 6]

const users = [
  { name: 'Alice', age: 30, active: true },
  { name: 'Bob', age: 25, active: false },
  { name: 'Charlie', age: 35, active: true }
];

// Keep active users over 30
const active_seniors = users.filter(user => user.active && user.age >= 30);
```

### reduce - Accumulate a Value

```javascript
const numbers = [1, 2, 3, 4, 5];

// Sum
const total = numbers.reduce((sum, n) => sum + n, 0);
// 15

// Product
const product = numbers.reduce((prod, n) => prod * n, 1);
// 120

const items = [
  { name: 'Apple', price: 1.5 },
  { name: 'Banana', price: 0.8 }
];

// Calculate total price
const total_price = items.reduce((sum, item) => sum + item.price, 0);
```

### find - Get First Match

```javascript
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' }
];

const alice = users.find(user => user.name === 'Alice');
// { id: 1, name: 'Alice' }

const missing = users.find(user => user.id === 99);
// undefined
```

### some / every - Test Conditions

```javascript
const numbers = [1, 2, 3, 4, 5];

// Check if any number is even
const has_even = numbers.some(n => n % 2 === 0);
// true

// Check if all numbers are positive
const all_positive = numbers.every(n => n > 0);
// true

const users = [
  { name: 'Alice', active: true },
  { name: 'Bob', active: false }
];

const has_inactive = users.some(user => !user.active);
// true

const all_active = users.every(user => user.active);
// false
```

## Chaining Methods

Combine multiple operations:

```javascript
const users = [
  { name: 'alice', age: 30, active: true },
  { name: 'bob', age: 25, active: false },
  { name: 'charlie', age: 35, active: true },
  { name: 'diana', age: 28, active: true }
];

const result = users
  .filter(user => user.active)              // Keep active users
  .filter(user => user.age >= 30)           // Keep 30+
  .map(user => user.name)                   // Extract names
  .map(name => name.toUpperCase())          // Uppercase
  .sort();                                  // Sort alphabetically

console.log(result); // ['ALICE', 'CHARLIE']
```

## Declarative vs Imperative

```javascript
// Imperative - how to do it
function getActiveUserNames(users) {
  const names = [];
  for (let i = 0; i < users.length; i++) {
    if (users[i].active) {
      names.push(users[i].name);
    }
  }
  return names;
}

// Declarative - what to do
function getActiveUserNames(users) {
  return users
    .filter(user => user.active)
    .map(user => user.name);
}
```

## Advanced reduce Examples

### Group by Property

```javascript
const users = [
  { name: 'Alice', role: 'admin' },
  { name: 'Bob', role: 'user' },
  { name: 'Charlie', role: 'admin' }
];

const by_role = users.reduce((groups, user) => {
  const role = user.role;
  if (!groups[role]) {
    groups[role] = [];
  }
  groups[role].push(user);
  return groups;
}, {});

// { admin: [...], user: [...] }
```

### Count Occurrences

```javascript
const fruits = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple'];

const counts = fruits.reduce((acc, fruit) => {
  acc[fruit] = (acc[fruit] || 0) + 1;
  return acc;
}, {});

// { apple: 3, banana: 2, orange: 1 }
```

### Flatten Arrays

```javascript
const nested = [[1, 2], [3, 4], [5, 6]];

const flattened = nested.reduce((acc, arr) => acc.concat(arr), []);
// [1, 2, 3, 4, 5, 6]

// Or use flat()
const flattened = nested.flat();
```

## Performance Considerations

For long chains, combine operations into one pass:

```javascript
// Less efficient - multiple iterations
const result = users
  .filter(user => user.active)
  .map(user => user.name)
  .filter(name => name.length > 5);

// More efficient - single iteration
const result = users.reduce((acc, user) => {
  if (user.active && user.name.length > 5) {
    acc.push(user.name);
  }
  return acc;
}, []);
```

## Related Notes
- [Function Composition](/architecture/functional/function-composition.md)
- [Immutability Patterns](/architecture/functional/immutability-patterns.md)
- [Pure Functions](/architecture/functional/pure-functions.md)
- [Functional Programming](/principles/functional-programming.md)
