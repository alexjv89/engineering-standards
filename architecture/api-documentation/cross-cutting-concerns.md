# Cross-Cutting Concerns

Document API-wide patterns in dedicated pages.

## What's Cross-Cutting?

Patterns that apply to all or most endpoints:
- Authentication
- Pagination
- Field selection
- Error handling
- Rate limiting

## Standard Cross-Cutting Docs

### authentication.mdx

```markdown
# Authentication

All API requests require authentication using API keys.

## API Key Headers

| Header | Description |
|--------|-------------|
| `api-key` | Your API key |
| `api-secret` | Your API secret |

## Example

\`\`\`bash
curl -X GET "https://api.example.com/api/v1/resource" \
  -H "api-key: xxx" \
  -H "api-secret: xxx"
\`\`\`

## Permission Matrix

| Resource | read | create | edit | delete |
|----------|------|--------|------|--------|
| transactions | Yes/No | Yes/No | Yes/No | Yes/No |

## Error Responses

[Show auth-specific errors]
```

### pagination.mdx

```markdown
# Pagination & Filtering

## Pagination Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number |
| limit | number | 100 | Items per page |

## Response Format

\`\`\`json
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
\`\`\`

## Field Selection

[Explain ?fields= parameter]

## Filtering

[List filter params per resource]
```

### errors.mdx

```markdown
# Error Codes

## Error Response Format

\`\`\`json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message"
  }
}
\`\`\`

## HTTP Status Codes

| Code | Meaning |
|------|---------|
| 200 | Success |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |

## Error Code Reference

[List all error codes with examples]
```

## Linking from Endpoint Docs

Reference cross-cutting docs instead of repeating:

```markdown
## Response Fields

See [Transaction Object](/docs/objects/transaction) for field descriptions.

## Pagination

See [Pagination & Filtering](/docs/api/pagination) for pagination details.
```

## Related Notes

- [Documentation Structure](/architecture/api-documentation/documentation-structure.md)
- [Endpoint Doc Template](/architecture/api-documentation/endpoint-doc-template.md)
- [Error Response Format](/architecture/api/error-response-format.md)
