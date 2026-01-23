# Field Selection

Allow clients to request specific fields via query parameter.

## Request Format

```
GET /api/v1/transactions?fields=id,date,inflow,outflow
```

Comma-separated list of field names.

## Response

Only requested fields returned:

```json
{
  "data": [
    {
      "id": "abc123",
      "date": "2025-01-05",
      "inflow": 1000.50,
      "outflow": 0
    }
  ],
  "page_context": { ... }
}
```

## Benefits

- **Reduced payload**: Less data over the wire
- **Faster responses**: Less serialization
- **Client control**: Fetch only what's needed
- **LLM-friendly**: Reduce token usage

## Implementation

Define allowed fields per resource:

```javascript
const PUBLIC_FIELDS = ['id', 'date', 'particulars', 'inflow', 'outflow', 'balance', 'entity', 'category'];
```

Parse and filter:

```javascript
const selected = parseFieldSelection(request, PUBLIC_FIELDS);
const response = selectFields(item, selected);
```

## Default Behavior

- No `fields` param: Return all public fields
- Invalid field names: Silently ignored
- Empty `fields`: Return all public fields

## Available Fields Per Resource

Document available fields in API docs:

**Transactions**: `id`, `date`, `particulars`, `inflow`, `outflow`, `balance`, `entity`, `category`, `type`, `account`, `account_name`

**Accounts**: `id`, `name`, `category`, `description`, `acc_no`, `currency`, `is_archived`

## Related Notes

- [Public Fields Constant](/architecture/api/public-fields-constant.md)
- [Field Selection Utilities](/architecture/api/field-selection-utilities.md)
- [Pagination Pattern](/architecture/api/pagination-pattern.md)
