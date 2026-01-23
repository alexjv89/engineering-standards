# Curl Examples Pattern

Write clear, copy-pasteable curl examples.

## Standard Format

```bash
curl -X [METHOD] "[URL]" \
  -H "api-key: fob_txn_xxxxxxxxxxxx" \
  -H "api-secret: xxxxxxxxxxxxxxxx" \
  -H "Content-Type: application/json" \
  -d '{
    "field": "value"
  }'
```

## Components

| Part | When to Include |
|------|-----------------|
| `-X METHOD` | Always (even for GET for clarity) |
| URL in quotes | Always |
| Auth headers | Always |
| Content-Type | POST/PUT only |
| `-d '{...}'` | POST/PUT only |

## GET Example

```bash
curl -X GET "https://api.example.com/api/v1/transactions?page=1&limit=10" \
  -H "api-key: fob_txn_xxxxxxxxxxxx" \
  -H "api-secret: xxxxxxxxxxxxxxxx"
```

## POST Example

```bash
curl -X POST "https://api.example.com/api/v1/transactions" \
  -H "api-key: fob_txn_xxxxxxxxxxxx" \
  -H "api-secret: xxxxxxxxxxxxxxxx" \
  -H "Content-Type: application/json" \
  -d '{
    "date": "2025-01-05",
    "account_id": "acc123",
    "inflow": 1000.50,
    "particulars": "Invoice payment"
  }'
```

## DELETE Example

```bash
curl -X DELETE "https://api.example.com/api/v1/transactions/abc123" \
  -H "api-key: fob_txn_xxxxxxxxxxxx" \
  -H "api-secret: xxxxxxxxxxxxxxxx"
```

## Multi-Line JSON

Use readable formatting with line breaks:

```bash
-d '{
    "name": "Main Account",
    "category": "bank",
    "currency": "USD",
    "opening_balance": 5000.00
  }'
```

Not compressed:
```bash
-d '{"name":"Main Account","category":"bank"}'
```

## Placeholder Values

Use consistent placeholders:
- API key: `fob_txn_xxxxxxxxxxxx`
- API secret: `xxxxxxxxxxxxxxxx`
- IDs: `abc123`, `acc123`, `stmt123`
- Dates: `2025-01-05` (use realistic dates)

## Notes After Examples

Add clarifying notes when needed:

```markdown
\`\`\`bash
curl -X POST ... -d '{ "inflow": 1000500 }'
\`\`\`

*Note: `inflow: 1000500` = 1000.50 in currency units (milli-units)*
```

## Multiple Examples

Label examples clearly:

```markdown
## Example: Filter by Date Range

\`\`\`bash
curl -X GET "...?date_from=2025-01-01&date_to=2025-01-31" ...
\`\`\`

## Example: Filter by Account

\`\`\`bash
curl -X GET "...?account=acc123" ...
\`\`\`
```

## Related Notes

- [Endpoint Doc Template](/architecture/api-documentation/endpoint-doc-template.md)
- [Documentation Structure](/architecture/api-documentation/documentation-structure.md)
