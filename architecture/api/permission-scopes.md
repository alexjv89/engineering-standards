# Permission Scopes

Define granular permissions using a resource:action matrix.

## Permission Structure

```javascript
{
  "transactions": { "read": true, "create": true, "edit": true, "delete": false },
  "accounts": { "read": true, "create": false, "edit": false, "delete": false },
  "rules": { "read": true, "create": true, "edit": true, "delete": true },
  "statements": { "read": true, "create": false, "edit": false, "delete": false }
}
```

## Actions

| Action | HTTP Method | Description |
|--------|-------------|-------------|
| read | GET | List and get single |
| create | POST | Create new |
| edit | PUT | Update existing |
| delete | DELETE | Remove |

## Permission Presets

```javascript
export const PERMISSION_PRESETS = {
  full_access: {
    transactions: { read: true, create: true, edit: true, delete: true },
    accounts: { read: true, create: true, edit: true, delete: true },
    rules: { read: true, create: true, edit: true, delete: true },
    statements: { read: true, create: true, edit: true, delete: true }
  },
  read_only: {
    transactions: { read: true, create: false, edit: false, delete: false },
    accounts: { read: true, create: false, edit: false, delete: false },
    rules: { read: true, create: false, edit: false, delete: false },
    statements: { read: true, create: false, edit: false, delete: false }
  },
  transactions_only: {
    transactions: { read: true, create: true, edit: true, delete: true },
    accounts: { read: true, create: false, edit: false, delete: false },
    rules: { read: false, create: false, edit: false, delete: false },
    statements: { read: true, create: false, edit: false, delete: false }
  }
};
```

## Check Permission

```javascript
export function hasPermission(api_key, model, action) {
  const scopes = api_key.scopes || PERMISSION_PRESETS.full_access;

  if (!scopes[model]) {
    return false;
  }

  return scopes[model][action] === true;
}
```

## Error Message Format

```
API key lacks {model}:{action} permission
```

Example: `API key lacks transactions:create permission`

## Storage

Store scopes as JSONB in the API keys table:

```sql
CREATE TABLE api_keys (
  id SERIAL PRIMARY KEY,
  key VARCHAR(255),
  secret VARCHAR(255),
  scopes JSONB,
  org VARCHAR(255),
  expires_at TIMESTAMP
);
```

## Related Notes

- [Authentication Middleware](/architecture/api/authentication-middleware.md)
- [HTTP Method Mapping](/architecture/api/http-method-mapping.md)
- [Error Response Format](/architecture/api/error-response-format.md)
