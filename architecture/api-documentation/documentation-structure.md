# API Documentation Structure

Organize API docs into cross-cutting concerns and per-resource endpoints.

## Directory Structure

```
content/docs/api/
├── authentication.mdx        # Cross-cutting: auth
├── pagination.mdx            # Cross-cutting: pagination & filtering
├── errors.mdx                # Cross-cutting: error codes
├── llm-access.mdx            # Cross-cutting: LLM consumption
│
└── endpoints/
    ├── transactions/
    │   ├── index.mdx         # Resource overview + endpoint table
    │   ├── list.mdx          # GET /transactions
    │   ├── get.mdx           # GET /transactions/:id
    │   ├── create.mdx        # POST /transactions
    │   ├── update.mdx        # PUT /transactions/:id
    │   └── delete.mdx        # DELETE /transactions/:id
    │
    ├── accounts/
    │   ├── index.mdx
    │   ├── list.mdx
    │   ├── get.mdx
    │   ├── create.mdx
    │   ├── update.mdx
    │   └── delete.mdx
    │
    └── rules/
        └── ...
```

## Cross-Cutting vs Per-Endpoint

**Cross-cutting** (applies to all endpoints):
- Authentication (API keys, headers)
- Pagination (page, limit, page_context)
- Field selection (?fields=)
- Error codes and formats
- LLM access patterns

**Per-endpoint** (specific to one operation):
- Request parameters
- Request body fields
- Response shape
- Specific errors

## Resource Index Page

Each resource has an `index.mdx` listing all operations:

```markdown
# Transactions API

The Transactions API allows you to manage financial transactions.

## Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/transactions` | List all transactions |
| GET | `/api/v1/transactions/:id` | Get a single transaction |
| POST | `/api/v1/transactions` | Create a new transaction |
| PUT | `/api/v1/transactions/:id` | Update a transaction |
| DELETE | `/api/v1/transactions/:id` | Delete a transaction |

## Quick Example

\`\`\`bash
curl -X GET "https://api.example.com/api/v1/transactions" \
  -H "api-key: xxx" \
  -H "api-secret: xxx"
\`\`\`
```

## Why This Structure?

- **MECE**: Each doc covers one thing (no overlap)
- **Navigable**: Clear hierarchy
- **Linkable**: Reference specific operations
- **LLM-friendly**: Fetch only needed docs

## Related Notes

- [Endpoint Doc Template](/architecture/api-documentation/endpoint-doc-template.md)
- [Cross-Cutting Concerns](/architecture/api-documentation/cross-cutting-concerns.md)
- [LLM Access Endpoints](/architecture/api-documentation/llm-access-endpoints.md)
