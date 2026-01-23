# Next.js Route Organization

Structure API routes using Next.js App Router conventions.

## Directory Structure

```
src/app/api/v1/
├── transactions/
│   ├── route.js              # GET (list), POST (create)
│   ├── [id]/
│   │   └── route.js          # GET (one), PUT (update), DELETE
│   └── search/
│       └── route.js          # POST (search)
├── accounts/
│   ├── route.js
│   └── [id]/
│       └── route.js
└── rules/
    ├── route.js
    └── [id]/
        └── route.js
```

## Route File Pattern

**Collection route** (`/transactions/route.js`):
```javascript
export async function GET(request) {
  // List resources
}

export async function POST(request) {
  // Create resource
}
```

**Item route** (`/transactions/[id]/route.js`):
```javascript
export async function GET(request, { params }) {
  const { id } = await params;
  // Get single resource
}

export async function PUT(request, { params }) {
  const { id } = await params;
  // Update resource
}

export async function DELETE(request, { params }) {
  const { id } = await params;
  // Delete resource
}
```

## Accessing Path Parameters

```javascript
export async function GET(request, { params }) {
  const { id } = await params;  // Note: await params in Next.js 15+
  // ...
}
```

## Accessing Query Parameters

```javascript
export async function GET(request) {
  const url = new URL(request.url);
  const page = url.searchParams.get('page');
  const limit = url.searchParams.get('limit');
  // ...
}
```

## Accessing Request Body

```javascript
export async function POST(request) {
  let body;
  try {
    body = await request.json();
  } catch (e) {
    return errorResponse('Invalid JSON body', 'INVALID_JSON', 400);
  }
  // ...
}
```

## File Naming

- Always `route.js` (not `route.ts`)
- Dynamic segments in brackets: `[id]`
- Nested folders for nested routes

## Related Notes

- [REST URL Structure](/architecture/api/rest-url-structure.md)
- [HTTP Method Mapping](/architecture/api/http-method-mapping.md)
- [API Versioning](/architecture/api/api-versioning.md)
