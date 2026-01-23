# Pagination Utilities

Parse pagination parameters from request URL.

## Function

```javascript
/**
 * Parse pagination parameters from request URL
 * @param {Request} request - The incoming request
 * @param {Object} [defaults] - Default values
 * @returns {{ page: number, limit: number, offset: number }}
 */
export function parsePagination(request, defaults = {}) {
  const url = new URL(request.url);

  const page = Math.max(1, parseInt(
    url.searchParams.get('page') || String(defaults.page || 1)
  ));

  const max_limit = defaults.max_limit || 500;
  const limit = Math.min(max_limit, Math.max(1, parseInt(
    url.searchParams.get('limit') || String(defaults.limit || 100)
  )));

  const offset = (page - 1) * limit;

  return { page, limit, offset };
}
```

## Usage

```javascript
export async function GET(request) {
  const { page, limit, offset } = parsePagination(request);

  const items = await db.Items.findAll({
    offset,
    limit,
    order: [['created_at', 'DESC']]
  });

  const total = await db.Items.count();

  return listResponse(items, { page, per_page: limit, total });
}
```

## With Custom Defaults

```javascript
const { page, limit, offset } = parsePagination(request, {
  page: 1,
  limit: 50,      // Default 50 instead of 100
  max_limit: 200  // Max 200 instead of 500
});
```

## Validation Behavior

| Input | Result |
|-------|--------|
| `page=0` | page = 1 (minimum) |
| `page=-5` | page = 1 (minimum) |
| `limit=0` | limit = 1 (minimum) |
| `limit=1000` | limit = 500 (capped at max) |
| Missing params | Uses defaults |

## Offset Calculation

```javascript
offset = (page - 1) * limit

// Page 1, limit 100 → offset 0
// Page 2, limit 100 → offset 100
// Page 3, limit 50  → offset 100
```

## Related Notes

- [Pagination Pattern](/architecture/api/pagination-pattern.md)
- [Response Helper Functions](/architecture/api/response-helper-functions.md)
- [Field Selection Utilities](/architecture/api/field-selection-utilities.md)
