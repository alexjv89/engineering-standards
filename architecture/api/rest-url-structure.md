# REST URL Structure

Use consistent URL patterns for API endpoints.

## Pattern

```
/api/v1/{resource}           # Collection
/api/v1/{resource}/{id}      # Individual item
/api/v1/{resource}/{id}/{sub-resource}  # Nested resource
```

## Examples

| URL | Description |
|-----|-------------|
| `/api/v1/transactions` | All transactions |
| `/api/v1/transactions/abc123` | Single transaction |
| `/api/v1/accounts` | All accounts |
| `/api/v1/accounts/def456` | Single account |
| `/api/v1/transactions/search` | Special action on collection |

## Naming Rules

- **Plural nouns**: `transactions`, `accounts`, `rules` (not `transaction`)
- **Lowercase**: No camelCase in URLs
- **Hyphens**: Use hyphens for multi-word resources (`work-records`, not `workRecords`)
- **No verbs**: URLs represent resources, not actions

## Query Parameters

Use query params for:
- Filtering: `?category=income&date_from=2025-01-01`
- Pagination: `?page=2&limit=50`
- Field selection: `?fields=id,name,date`
- Sorting: `?sort_order=ASC`

## Special Endpoints

For operations that don't fit CRUD:
```
POST /api/v1/transactions/search    # Complex search
POST /api/v1/statements/process     # Trigger processing
```

Use POST for actions that have side effects or complex request bodies.

## Related Notes

- [HTTP Method Mapping](/architecture/api/http-method-mapping.md)
- [API Versioning](/architecture/api/api-versioning.md)
- [Pagination Pattern](/architecture/api/pagination-pattern.md)
