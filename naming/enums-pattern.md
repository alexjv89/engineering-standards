# Enum-Like Objects: PascalCase with SCREAMING_SNAKE_CASE Values

Create enum-like objects using `PascalCase` for the object name and `SCREAMING_SNAKE_CASE` for keys.

## Pattern

```javascript
const UserRole = {
  ADMIN: 'admin',
  USER: 'user',
  GUEST: 'guest'
};

const OrderStatus = {
  PENDING: 'pending',
  PROCESSING: 'processing',
  COMPLETED: 'completed',
  CANCELLED: 'cancelled'
};

// Freeze to prevent modification
Object.freeze(UserRole);
Object.freeze(OrderStatus);
```

## Usage

```javascript
function checkPermission(role) {
  if (role === UserRole.ADMIN) {
    return true;
  }
  return false;
}

const status = OrderStatus.PENDING;
```

## Why Object.freeze?

Prevents accidental modification:

```javascript
const Status = { ACTIVE: 'active', INACTIVE: 'inactive' };
Object.freeze(Status);

// This will fail silently (or throw in strict mode)
Status.DELETED = 'deleted';
Status.ACTIVE = 'something else';
```

## With JSDoc Documentation

```javascript
/**
 * @typedef {Object} UserRole
 * @property {'admin'} ADMIN - Administrator role
 * @property {'user'} USER - Regular user role
 * @property {'guest'} GUEST - Guest user role
 */
const UserRole = {
  ADMIN: 'admin',
  USER: 'user',
  GUEST: 'guest'
};

Object.freeze(UserRole);
```

## Reverse Lookup

Add helper method if needed:

```javascript
const Status = {
  ACTIVE: 'active',
  INACTIVE: 'inactive',

  fromValue(value) {
    return Object.keys(this).find(key => this[key] === value);
  }
};

Object.freeze(Status);
console.log(Status.fromValue('active')); // 'ACTIVE'
```

## Related Notes
- [Constants: SCREAMING_SNAKE_CASE](/naming/constants-screaming-snake.md)
- [JSDoc Type Patterns](/architecture/jsdoc/syntax/jsdoc-types.md)
- [Variables: snake_case](/naming/variables-snake-case.md)
