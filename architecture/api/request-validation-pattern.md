# Request Validation Pattern

Validate request body explicitly in route handlers.

## Pattern

```javascript
export async function POST(request) {
  const { auth, response } = await authenticateRequest(request, 'transactions', 'create');
  if (response) return response;

  // 1. Parse JSON
  let body;
  try {
    body = await request.json();
  } catch (e) {
    return errorResponse('Invalid JSON body', 'INVALID_JSON', 400);
  }

  // 2. Validate required fields
  if (!body.date) {
    return errorResponse('date is required', 'VALIDATION_ERROR', 400);
  }
  if (!body.account) {
    return errorResponse('account is required', 'VALIDATION_ERROR', 400);
  }

  // 3. Validate enums
  if (body.category && !VALID_CATEGORIES.includes(body.category)) {
    return errorResponse(
      `category must be one of: ${VALID_CATEGORIES.join(', ')}`,
      'VALIDATION_ERROR',
      400
    );
  }

  // 4. Validate types
  if (body.priority !== undefined && typeof body.priority !== 'number') {
    return errorResponse('priority must be a number', 'VALIDATION_ERROR', 400);
  }

  // 5. Validate references
  const account = await db.Accounts.findOne({
    where: { id: body.account, org: auth.org.id }
  });
  if (!account) {
    return errorResponse(
      'Account not found or does not belong to this organization',
      'VALIDATION_ERROR',
      400
    );
  }

  // 6. Create resource
  const item = await db.Items.create({ ... });
  return successResponse(item, 201);
}
```

## Validation Order

1. **JSON parsing** - Catch malformed JSON
2. **Required fields** - Check presence
3. **Conditional requirements** - Either/or fields
4. **Enum validation** - Value in allowed list
5. **Type validation** - Correct data type
6. **Reference validation** - Foreign keys exist

## Define Valid Values

```javascript
const VALID_CATEGORIES = ['bank', 'credit_card'];
const VALID_UPLOAD_FREQUENCIES = ['daily', 'weekly', 'monthly', 'quarterly', 'manual'];
```

## Conditional Requirements

```javascript
if (body.inflow === undefined && body.outflow === undefined) {
  return errorResponse(
    'Either inflow or outflow is required',
    'VALIDATION_ERROR',
    400
  );
}
```

## Update Validation

For PUT, validate only provided fields:

```javascript
export async function PUT(request, { params }) {
  // ... auth and find existing ...

  // Only validate if field is provided
  if (body.category !== undefined && !VALID_CATEGORIES.includes(body.category)) {
    return errorResponse(`category must be one of: ${VALID_CATEGORIES.join(', ')}`, 'VALIDATION_ERROR', 400);
  }

  // Build update object with allowed fields only
  const updates = {};
  const allowed = ['particulars', 'entity', 'category', 'comments'];

  for (const field of allowed) {
    if (body[field] !== undefined) {
      updates[field] = body[field];
    }
  }

  await item.update(updates);
}
```

## Related Notes

- [Validation Error Pattern](/architecture/api/validation-error-pattern.md)
- [Error Response Format](/architecture/api/error-response-format.md)
- [Response Helper Functions](/architecture/api/response-helper-functions.md)
