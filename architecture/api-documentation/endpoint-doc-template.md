# Endpoint Doc Template

Use consistent structure for all endpoint documentation.

## Template

```markdown
---
title: [Verb] [Resource]
description: [One-line description]
---

# [Verb] [Resource]

\`\`\`
[METHOD] /api/v1/[resource]
\`\`\`

[One sentence describing what this endpoint does.]

## Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| id | string | [Resource] ID |

## Query Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number |
| limit | number | 100 | Items per page (max 500) |
| fields | string | all | Comma-separated field names |

## Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| name | string | Yes | Display name |
| category | string | No | Category (enum: a, b, c) |

## Example Request

\`\`\`bash
curl -X [METHOD] "https://api.example.com/api/v1/[resource]" \
  -H "api-key: xxx" \
  -H "api-secret: xxx" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Example"
  }'
\`\`\`

## Response

\`\`\`json
{
  "data": {
    "id": "abc123",
    "name": "Example"
  }
}
\`\`\`

## Errors

| Code | Description |
|------|-------------|
| 400 | Validation error |
| 401 | Invalid or missing API credentials |
| 403 | API key lacks `[resource]:[action]` permission |
| 404 | [Resource] not found |
```

## Section Rules

| Section | When to Include |
|---------|-----------------|
| Path Parameters | GET/PUT/DELETE with `:id` |
| Query Parameters | GET (list) endpoints |
| Request Body | POST/PUT endpoints |
| Example Request | Always |
| Response | Always |
| Errors | Always |

## Frontmatter

```yaml
---
title: Create Transaction
description: Create a new transaction manually
---
```

- **title**: Verb + Resource (Create Transaction, List Accounts)
- **description**: One line, starts with verb

## Multiple Examples

For complex endpoints, show multiple use cases:

```markdown
## Example: Create Income Transaction

\`\`\`bash
curl -X POST ...
\`\`\`

## Example: Create Expense Transaction

\`\`\`bash
curl -X POST ...
\`\`\`
```

## Related Notes

- [Curl Examples Pattern](/architecture/api-documentation/curl-examples-pattern.md)
- [Documentation Structure](/architecture/api-documentation/documentation-structure.md)
- [Cross-Cutting Concerns](/architecture/api-documentation/cross-cutting-concerns.md)
