# Field Selection Utilities

Parse and apply field selection from request URL.

## Parse Function

```javascript
/**
 * Parse field selection from request URL
 * @param {Request} request - The incoming request
 * @param {string[]} allowed_fields - List of allowed field names
 * @returns {string[]|null} - Selected fields or null for all
 */
export function parseFieldSelection(request, allowed_fields) {
  const url = new URL(request.url);
  const fields_param = url.searchParams.get('fields');

  if (!fields_param) {
    return null;  // Return all fields
  }

  const requested = fields_param.split(',').map(f => f.trim());
  return requested.filter(f => allowed_fields.includes(f));
}
```

## Select Function

```javascript
/**
 * Filter object to only include specified fields
 * @param {Object} obj - Object to filter
 * @param {string[]|null} fields - Fields to include (null for all)
 * @returns {Object}
 */
export function selectFields(obj, fields) {
  if (!fields) {
    return obj;
  }

  const result = {};
  for (const field of fields) {
    if (field in obj) {
      result[field] = obj[field];
    }
  }
  return result;
}
```

## Usage

```javascript
const PUBLIC_FIELDS = ['id', 'date', 'particulars', 'inflow', 'outflow', 'entity', 'category'];

export async function GET(request) {
  const selected_fields = parseFieldSelection(request, PUBLIC_FIELDS);

  const items = await db.Items.findAll({ ... });

  const formatted = items.map(item => {
    const f = formatItem(item);
    return selectFields(f, selected_fields);
  });

  return listResponse(formatted, { ... });
}
```

## Behavior

| Request | Result |
|---------|--------|
| No `fields` param | All PUBLIC_FIELDS returned |
| `?fields=id,date` | Only id and date |
| `?fields=id,invalid` | Only id (invalid ignored) |
| `?fields=` | All PUBLIC_FIELDS (empty treated as no selection) |

## Order of Operations

1. Fetch from database
2. Format for API (via `formatEntity()`)
3. Apply field selection (via `selectFields()`)
4. Return response

This ensures field selection works on the API shape, not the database shape.

## Related Notes

- [Field Selection](/architecture/api/field-selection.md)
- [Public Fields Constant](/architecture/api/public-fields-constant.md)
- [Format Entity Pattern](/architecture/api/format-entity-pattern.md)
