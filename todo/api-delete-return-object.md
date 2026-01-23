# API: DELETE Returns Deleted Object

## Current
DELETE returns `204 No Content` with empty body.

## Proposed
Return the deleted object in response body with `200 OK`.

```json
{
  "data": {
    "id": "abc123",
    "name": "Deleted Item",
    ...
  }
}
```

## Why
- Client gets confirmation of what was deleted
- Useful for undo functionality
- Useful for audit logging on client side

## Files to Update
- `/architecture/api/http-method-mapping.md`
- `/architecture/api/response-helper-functions.md`
