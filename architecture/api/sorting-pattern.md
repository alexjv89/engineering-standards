# Sorting

List endpoints take `sort_by` (a whitelisted column) and `sort_order` (`ASC` | `DESC`), state their defaults, and always append a stable tiebreaker.

## Pattern

```
GET /api/v1/transactions?sort_by=updated_at&sort_order=ASC
```

| Parameter | Values | Default |
|-----------|--------|---------|
| `sort_by` | One of an explicit per-endpoint whitelist, e.g. `date`, `created_at`, `updated_at` | Stated per endpoint (usually `date` or `created_at`) |
| `sort_order` | `ASC` or `DESC` (case-insensitive) | `DESC` |

```javascript
const SORTABLE = ['date', 'created_at', 'updated_at'];

const sort_by = params.get('sort_by') || 'date';
if (!SORTABLE.includes(sort_by)) {
  return errorResponse(`sort_by must be one of: ${SORTABLE.join(', ')}`, 'VALIDATION_ERROR', 400);
}
const sort_order = (params.get('sort_order') || 'DESC').toUpperCase();
if (!['ASC', 'DESC'].includes(sort_order)) {
  return errorResponse('sort_order must be ASC or DESC', 'VALIDATION_ERROR', 400);
}

order: [[sort_by, sort_order], ['id', sort_order]]   // tiebreaker keeps pagination stable
```

## Rules

- **Whitelist, never pass-through.** `sort_by` is compared against a constant list; the raw string never reaches `ORDER BY`.
- **Always a tiebreaker.** Rows equal on the sort column would otherwise shuffle between pages; append `id` (or `created_at, id`).
- **Sorting is explicit.** A filter such as `updated_at_from` does not silently change the order. A consumer walking a watermark passes `sort_by=updated_at&sort_order=ASC` itself.
- **`sort_by` is a column, `sort_order` is a direction.** Don't reuse one for the other (an existing endpoint once read a direction out of `sort_by`, producing `ORDER BY date date`).

## Related Notes

- [Pagination Pattern](/architecture/api/pagination-pattern.md) — page-based pagination this ordering keeps stable
- [Date Range Filtering](/architecture/api/date-range-filtering.md) — the filters most often paired with `sort_by`
- [REST URL Structure](/architecture/api/rest-url-structure.md) — query parameter categories
