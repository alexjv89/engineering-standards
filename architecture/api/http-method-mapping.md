# HTTP Method Mapping

Map HTTP methods to CRUD operations consistently.

## Standard Mapping

| Method | Operation | URL Pattern | Response |
|--------|-----------|-------------|----------|
| GET | List | `/resources` | 200 + array |
| GET | Read | `/resources/{id}` | 200 + object |
| POST | Create | `/resources` | 201 + created object |
| PUT | Update | `/resources/{id}` | 200 + updated object |
| DELETE | Delete | `/resources/{id}` | 204 No Content |

## Method Semantics

**GET** - Read-only, no side effects
- Safe to retry
- Cacheable
- No request body

**POST** - Create new resource
- Not idempotent (creates new each time)
- Returns created resource with generated ID

**PUT** - Replace/update resource
- Idempotent (same result if repeated)
- Partial updates: only provided fields change
- Returns updated resource

**DELETE** - Remove resource
- Idempotent
- Returns 204 with no body

## Permission Mapping

| Method | Permission Action |
|--------|-------------------|
| GET | `read` |
| POST | `create` |
| PUT | `edit` |
| DELETE | `delete` |

## Special Cases

**Search with complex criteria:**
```
POST /api/v1/transactions/search
```
Use POST when query params would be unwieldy.

**Bulk operations:**
```
POST /api/v1/transactions/bulk-delete
```
Explicit action endpoint for bulk operations.

## Related Notes

- [REST URL Structure](/architecture/api/rest-url-structure.md)
- [Permission Scopes](/architecture/api/permission-scopes.md)
- [Next.js Route Organization](/architecture/api/nextjs-route-organization.md)
