# Arrow Functions vs Function Declarations

Choose between arrow functions and function declarations based on context and purpose.

## Guidelines

**Use arrow functions for:**
- Callbacks and inline functions
- Short, simple functions
- Preserving `this` context

**Use function declarations for:**
- Top-level named functions
- Functions with complex logic
- Functions that need hoisting

## Arrow Functions

```javascript
// Callbacks
const users = data.map(user => user.name);
const filtered = items.filter(item => item.active);

// Simple operations
const add = (a, b) => a + b;
const double = x => x * 2;

// Event handlers in React
function Component() {
  const handleClick = () => {
    console.log('clicked');
  };

  return <button onClick={handleClick}>Click</button>;
}
```

## Function Declarations

```javascript
// Top-level named functions
function processUserData(data) {
  // Complex logic with multiple steps
  const validated = validateData(data);
  const transformed = transformData(validated);
  return enrichData(transformed);
}

// Functions that need hoisting
function main() {
  helper(); // Can call before declaration
}

function helper() {
  console.log('helper');
}

// Named functions for stack traces
function calculateComplexValue() {
  // Error stack trace will show function name
}
```

## React Components

Both work, but arrow functions common for simple components:

```javascript
// Arrow function (common)
const Button = ({ children, onClick }) => {
  return <button onClick={onClick}>{children}</button>;
};

// Function declaration (also fine)
function Button({ children, onClick }) {
  return <button onClick={onClick}>{children}</button>;
}
```

## `this` Context in Event Handlers

Arrow functions don't have their own `this`, useful for event handlers in objects:

```javascript
const dataHandler = {
  data: [],

  // Arrow function preserves `this` from enclosing scope
  handleFetch: async function() {
    const result = await fetch('/api/data');
    // In regular function, need arrow in callback to preserve `this`
    result.json().then(data => {
      this.data = data;  // Arrow function preserves `this`
    });
  },

  // Or use arrow function for the whole method
  handleUpdate: async () => {
    const result = await fetch('/api/data');
    this.data = await result.json();
  }
};
```

**Note**: With functional programming, avoid relying on `this`. Use parameters instead:

```javascript
// Functional approach - no `this`
async function fetchAndStoreData(dataStore) {
  const result = await fetch('/api/data');
  const data = await result.json();
  return { ...dataStore, data };
}
```

## Related Notes
- [Functions: camelCase](/naming/functions-camelcase.md)
- [Functional Programming](/principles/functional-programming.md)
- [Pure Functions](/architecture/functional/pure-functions.md)
