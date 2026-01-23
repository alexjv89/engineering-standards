# Response Envelope Pattern

Wrap all API responses in a consistent envelope structure.

## Success Response

Single item:
```json
{
  "data": {
    "id": "abc123",
    "name": "Example"
  }
}
```

List of items:
```json
{
  "data": [
    { "id": "abc123", "name": "Example 1" },
    { "id": "def456", "name": "Example 2" }
  ],
  "page_context": {
    "count": 2,
    "page": 1,
    "per_page": 100,
    "total": 50,
    "has_more": false
  }
}
```

## Error Response

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "date is required",
    "details": {}
  }
}
```

## Why Envelope?

- **Extensibility**: Add metadata without breaking clients
- **Consistency**: Same structure for all endpoints
- **Clarity**: `data` vs `error` makes success/failure explicit
- **Pagination**: `page_context` lives alongside `data`

## HTTP Status Codes

| Status | Envelope |
|--------|----------|
| 200 | `{data: ...}` |
| 201 | `{data: ...}` |
| 204 | No body |
| 4xx | `{error: ...}` |
| 5xx | `{error: ...}` |

## Related Notes

- [Pagination Pattern](/architecture/api/pagination-pattern.md)
- [Error Response Format](/architecture/api/error-response-format.md)
- [Response Helper Functions](/architecture/api/response-helper-functions.md)
