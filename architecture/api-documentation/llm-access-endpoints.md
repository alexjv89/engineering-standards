# LLM Access Endpoints

Provide raw markdown endpoints for LLM consumption.

## Pattern

```
GET /docs/md/sitemap           # Index of all pages
GET /docs/md/{path}            # Raw markdown for specific page
```

Both return `text/markdown` content type.

## Sitemap Endpoint

Returns a markdown-formatted index of all documentation:

```markdown
# Documentation Sitemap

## API Reference
- [Authentication](/docs/md/api/authentication)
- [Pagination](/docs/md/api/pagination)
- [Errors](/docs/md/api/errors)

## Endpoints

### Transactions
- [List Transactions](/docs/md/api/endpoints/transactions/list)
- [Get Transaction](/docs/md/api/endpoints/transactions/get)
- [Create Transaction](/docs/md/api/endpoints/transactions/create)
...
```

## URL Pattern

```
/docs/md/{section}/{subsection}/{page}
```

Examples:
| URL | Content |
|-----|---------|
| `/docs/md/api/authentication` | Auth docs |
| `/docs/md/api/endpoints/transactions/list` | List endpoint |
| `/docs/md/objects/transaction` | Object reference |

## Implementation

Next.js API route that reads MDX and returns raw content:

```javascript
// src/app/docs/md/[...path]/route.js

import { readFile } from 'fs/promises';
import { join } from 'path';

export async function GET(request, { params }) {
  const { path } = await params;
  const file_path = join(process.cwd(), 'content/docs', ...path) + '.mdx';

  try {
    const content = await readFile(file_path, 'utf-8');
    return new Response(content, {
      headers: { 'Content-Type': 'text/markdown' }
    });
  } catch {
    return new Response('Not found', { status: 404 });
  }
}
```

## Why LLM Access?

- **Context injection**: Load relevant docs into LLM prompts
- **MCP integration**: Serve docs to Claude MCP servers
- **RAG systems**: Index docs for retrieval
- **Agent workflows**: Fetch docs on demand

## Usage Example

```javascript
// In an MCP server or agent
async function getApiDocs(endpoint) {
  const response = await fetch(
    `https://api.example.com/docs/md/api/endpoints/${endpoint}`
  );
  return response.text();
}

// Load docs for transactions list
const docs = await getApiDocs('transactions/list');
```

## Caching

Add cache headers for production:

```javascript
return new Response(content, {
  headers: {
    'Content-Type': 'text/markdown',
    'Cache-Control': 'public, max-age=3600'  // 1 hour
  }
});
```

## Related Notes

- [Documentation Structure](/architecture/api-documentation/documentation-structure.md)
- [Endpoint Doc Template](/architecture/api-documentation/endpoint-doc-template.md)
- [Brevity](/principles/documentation/brevity.md)
