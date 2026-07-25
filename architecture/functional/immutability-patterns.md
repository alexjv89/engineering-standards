# Immutability Patterns

Avoid mutating data structures; create new versions instead for predictability and safety.

## Why Immutability?

- **Predictable** - Data doesn't change unexpectedly
- **Debuggable** - Easier to track state changes
- **React-friendly** - Triggers re-renders correctly
- **Thread-safe** - Safe for concurrent operations

## Array Immutability

### Adding Items

```javascript
// Bad - mutates original array
function addItem(cart, item) {
  cart.push(item);  // Mutates cart
  return cart;
}

// Good - returns new array
function addItem(cart, item) {
  return [...cart, item];
}

// Alternative
function addItem(cart, item) {
  return cart.concat(item);
}
```

### Removing Items

```javascript
// Bad - mutates original
function removeItem(cart, index) {
  cart.splice(index, 1);
  return cart;
}

// Good - returns new array
function removeItem(cart, index) {
  return cart.filter((_, i) => i !== index);
}

// Or using slice
function removeItem(cart, index) {
  return [...cart.slice(0, index), ...cart.slice(index + 1)];
}
```

### Updating Items

```javascript
// Bad - mutates original
function updateItem(cart, index, new_item) {
  cart[index] = new_item;
  return cart;
}

// Good - returns new array
function updateItem(cart, index, new_item) {
  return cart.map((item, i) => i === index ? new_item : item);
}
```

## Object Immutability

### Updating Properties

```javascript
// Bad - mutates object
function updateUser(user, updates) {
  user.name = updates.name;
  return user;
}

// Good - returns new object
function updateUser(user, updates) {
  return { ...user, ...updates };
}

// Alternative - Object.assign
function updateUser(user, updates) {
  return Object.assign({}, user, updates);
}
```

### Nested Object Updates

```javascript
const user = {
  id: 1,
  profile: {
    name: 'Alice',
    settings: {
      theme: 'light'
    }
  }
};

// Bad - mutates nested object
function updateTheme(user, theme) {
  user.profile.settings.theme = theme;
  return user;
}

// Good - creates new objects at each level
function updateTheme(user, theme) {
  return {
    ...user,
    profile: {
      ...user.profile,
      settings: {
        ...user.profile.settings,
        theme
      }
    }
  };
}
```

## React State Immutability

```javascript
function ShoppingCart() {
  const [items, setItems] = useState([]);

  // Add item immutably
  const addItem = (item) => {
    setItems([...items, item]);
  };

  // Remove item immutably
  const removeItem = (index) => {
    setItems(items.filter((_, i) => i !== index));
  };

  // Update item immutably
  const updateItem = (index, new_item) => {
    setItems(items.map((item, i) => i === index ? new_item : item));
  };

  return <div>{/* render items */}</div>;
}
```

## Freezing Objects

Prevent accidental mutations in development:

```javascript
const config = Object.freeze({
  API_URL: 'https://api.example.com',
  TIMEOUT: 5000
});

// This will fail silently (or throw in strict mode)
config.API_URL = 'https://other.com';
config.NEW_KEY = 'value';

console.log(config.API_URL); // Still 'https://api.example.com'
```

## Deep Freeze for Nested Objects

```javascript
function deepFreeze(obj) {
  Object.freeze(obj);
  Object.getOwnPropertyNames(obj).forEach(prop => {
    if (obj[prop] !== null && typeof obj[prop] === 'object') {
      deepFreeze(obj[prop]);
    }
  });
  return obj;
}

const config = deepFreeze({
  api: {
    url: 'https://api.example.com',
    timeout: 5000
  }
});
```

## Immutability Libraries

For complex nested updates, use a library like Immer:

```javascript
// Using Immer
import produce from 'immer';

const next_state = produce(current_state, draft => {
  draft.user.profile.settings.theme = 'dark';
  draft.items.push(new_item);
});
```

## Related Notes
- [Pure Functions](/architecture/functional/pure-functions.md)
- [Array Functional Methods](/architecture/functional/array-methods-functional.md)
- [Functional Programming](/principles/functional-programming.md)
