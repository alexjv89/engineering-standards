# Pagination Pattern

Use page-based pagination with consistent page_context response.

## Request Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number (1-indexed) |
| limit | number | 100 | Items per page |

**Maximum limit**: 500 items per page

## Response Structure

```json
{
  "data": [...],
  "page_context": {
    "count": 25,
    "page": 1,
    "per_page": 100,
    "total": 250,
    "has_more": true
  }
}
```

## page_context Fields

| Field | Description |
|-------|-------------|
| count | Items in current page |
| page | Current page number |
| per_page | Requested items per page |
| total | Total items across all pages |
| has_more | `true` if more pages exist |

## Calculating has_more

```javascript
has_more: page * per_page < total
```

## Client Iteration Example

```javascript
async function getAllItems() {
  const items = [];
  let page = 1;
  let has_more = true;

  while (has_more) {
    const response = await fetch(`/api/v1/items?page=${page}&limit=100`);
    const data = await response.json();

    items.push(...data.data);
    has_more = data.page_context.has_more;
    page++;
  }

  return items;
}
```

## Why Page-Based?

- **Simple**: Easy to understand and implement
- **Stateless**: No cursor to maintain
- **Jumpable**: Can go directly to page N
- **Countable**: Total always available

## Related Notes

- [Response Envelope Pattern](/architecture/api/response-envelope.md)
- [Pagination Utilities](/architecture/api/pagination-utilities.md)
- [Field Selection](/architecture/api/field-selection.md)
