# Date Range Filtering

Filter a list endpoint on a date or timestamp column with `{column}_from` and `{column}_to` — both optional, both inclusive, named after the column they filter.

## Pattern

```
GET /api/v1/transactions?date_from=2026-08-01&date_to=2026-08-31
GET /api/v1/transactions?updated_at_from=2026-09-01T00:00:00Z
GET /api/v1/statements?created_at_from=2026-08-01T00:00:00Z&created_at_to=2026-08-31T23:59:59Z
```

| Rule | Detail |
|------|--------|
| Name | `{column}_from` / `{column}_to`, the column name verbatim (`date`, `created_at`, `updated_at`) |
| Bounds | Inclusive on both ends (`>=` / `<=`); either may be omitted |
| `DATE` column | `YYYY-MM-DD` |
| `TIMESTAMP` column | ISO 8601 instant with seconds and `Z` or an offset — `2026-09-01T00:00:00Z`, `2026-09-01T05:30:00+05:30`, fractional seconds allowed |
| Date-only on a timestamp | **Rejected** (`400`). The server never guesses a timezone or widens to end-of-day; a client wanting "the whole of Sept 1 in IST" sends the instants. Same rule as [AI Tool Date Validation](/architecture/ai/ai-tool-date-validation.md) |
| Validation | Malformed value or `from > to` → `400 VALIDATION_ERROR` with a message naming the parameter and the expected format. Never pass the raw string to the ORM (a bad date becomes a Postgres cast error, i.e. a `500`) |
| Ordering | Not implied by the filter — pair with [Sorting](/architecture/api/sorting-pattern.md) |

The format follows the **column type**, not the parameter. `date_from` on a `DATE` column stays `YYYY-MM-DD`; `created_at_from` on a `TIMESTAMPTZ` column is an instant. An endpoint that must accept a date on a timestamp column documents that as a per-endpoint exception; it is never the default.

## Reference helper

One helper per app, used by every route that filters on a date or timestamp:

```javascript
const DATE_RE = /^\d{4}-\d{2}-\d{2}$/;
const INSTANT_RE = /^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}(\.\d{1,6})?(Z|[+-]\d{2}:\d{2})$/;

// Date.parse alone is not enough: V8 rolls "2026-02-30" forward to March 2 instead of rejecting it.
function hasRealCalendarDate(value) {
  const [y, m, d] = value.slice(0, 10).split('-').map(Number);
  const probe = new Date(Date.UTC(y, m - 1, d));
  return probe.getUTCFullYear() === y && probe.getUTCMonth() === m - 1 && probe.getUTCDate() === d;
}

/**
 * Parse `{column}_from` / `{column}_to` into a Sequelize range, or an error response.
 * @param {URLSearchParams} params
 * @param {string} column            e.g. 'date', 'updated_at'
 * @param {{ type: 'date' | 'timestamp' }} opts
 * @returns {{ where?: object, response?: Response }}
 */
export function parseDateRange(params, column, { type }) {
  const re = type === 'date' ? DATE_RE : INSTANT_RE;
  const expected = type === 'date'
    ? 'a date in YYYY-MM-DD form'
    : 'an ISO 8601 UTC timestamp such as 2026-09-01T00:00:00Z';
  const bounds = {};
  for (const edge of ['from', 'to']) {
    const key = `${column}_${edge}`;
    const raw = (params.get(key) || '').trim();
    if (!raw) continue;
    if (!re.test(raw) || !hasRealCalendarDate(raw) || Number.isNaN(Date.parse(raw))) {
      return { response: errorResponse(`${key} expects ${expected}`, 'VALIDATION_ERROR', 400) };
    }
    bounds[edge] = raw;
  }
  if (bounds.from && bounds.to && Date.parse(bounds.from) > Date.parse(bounds.to)) {
    return { response: errorResponse(`${column}_from must be on or before ${column}_to`, 'VALIDATION_ERROR', 400) };
  }
  const where = {};
  if (bounds.from) where[Op.gte] = bounds.from;
  if (bounds.to) where[Op.lte] = bounds.to;
  return { where: Object.keys(where).length ? where : undefined };
}
```

## Not these

| Spelling | Why not |
|----------|---------|
| `updated_since`, `since`, `until` | No `_to` counterpart; "since" reads inclusive to some and exclusive to others |
| `created_after`, `created_before` | Ambiguous inclusivity |
| `start_date`, `end_date` | Prefix form; doesn't name the column |
| bare `from`, `to` | Collide with sender/recipient fields (see `fob-email`'s `--from`) |

Documented exceptions in the estate: `as_of` (a single point-in-time `<=`, not a range) and statements' `period_from`/`period_to` (an overlap test across two columns, so there is no single `{column}`).

## CLI flags

`--from`/`--to` for the resource's primary `date`; `--{column}-from`/`--{column}-to` for others, with a trailing `_at` dropped: `--updated-from`, `--created-to`. A CLI may accept a date-only value for a timestamp flag and convert it to an instant in the user's local zone before sending.

## Related Notes

- [REST URL Structure](/architecture/api/rest-url-structure.md) — where filters sit in the URL
- [Sorting](/architecture/api/sorting-pattern.md) — the companion for watermark-style consumers
- [Request Validation Pattern](/architecture/api/request-validation-pattern.md) — the `400 VALIDATION_ERROR` shape
- [AI Tool Date Validation](/architecture/ai/ai-tool-date-validation.md) — the same instant-only rule for AI tool params
- [Timestamp Columns](/architecture/database/timestamp-columns.md) — the columns these filters target
