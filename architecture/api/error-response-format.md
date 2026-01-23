# Error Response Format

Return errors in a consistent envelope structure.

## Structure

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message",
    "details": {}
  }
}
```

## Fields

| Field | Required | Description |
|-------|----------|-------------|
| code | Yes | Machine-readable error code (SCREAMING_SNAKE) |
| message | Yes | Human-readable description |
| details | No | Additional context (field errors, etc.) |

## Standard Error Codes

| Code | HTTP Status | Meaning |
|------|-------------|---------|
| UNAUTHORIZED | 401 | Missing or invalid credentials |
| FORBIDDEN | 403 | Valid credentials, insufficient permissions |
| NOT_FOUND | 404 | Resource doesn't exist |
| VALIDATION_ERROR | 400 | Invalid request data |
| INVALID_JSON | 400 | Malformed JSON body |
| BAD_REQUEST | 400 | Generic client error |
| INTERNAL_ERROR | 500 | Server error |

## Examples

**Authentication error:**
```json
{
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Invalid API credentials"
  }
}
```

**Permission error:**
```json
{
  "error": {
    "code": "FORBIDDEN",
    "message": "API key lacks transactions:create permission"
  }
}
```

**Validation error:**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "date is required"
  }
}
```

**Not found:**
```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "Transaction not found"
  }
}
```

## Client Handling

```javascript
if (!response.ok) {
  const { error } = await response.json();
  throw new Error(error.message);
}
```

## Related Notes

- [Response Envelope Pattern](/architecture/api/response-envelope.md)
- [Validation Error Pattern](/architecture/api/validation-error-pattern.md)
- [Response Helper Functions](/architecture/api/response-helper-functions.md)
