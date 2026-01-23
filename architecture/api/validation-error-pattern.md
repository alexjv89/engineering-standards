# Validation Error Pattern

Return clear, actionable validation errors.

## Single Field Error

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "date is required"
  }
}
```

## Field Naming in Messages

Use the field name as it appears in the API (which should match DB field names):

```javascript
// Good - matches API/DB field name
return errorResponse('account is required', 'VALIDATION_ERROR', 400);

// Bad - uses a different name than the API field
return errorResponse('account_id is required', 'VALIDATION_ERROR', 400);
```

## Validation Order

Check in order of importance:
1. Required fields first
2. Type validation
3. Enum/format validation
4. Business logic validation

```javascript
// Validate required fields
if (!body.date) {
  return errorResponse('date is required', 'VALIDATION_ERROR', 400);
}
if (!body.account) {
  return errorResponse('account is required', 'VALIDATION_ERROR', 400);
}

// Validate enums
if (body.category && !VALID_CATEGORIES.includes(body.category)) {
  return errorResponse(
    `category must be one of: ${VALID_CATEGORIES.join(', ')}`,
    'VALIDATION_ERROR',
    400
  );
}

// Validate types
if (body.priority !== undefined && typeof body.priority !== 'number') {
  return errorResponse('priority must be a number', 'VALIDATION_ERROR', 400);
}
```

## Conditional Requirements

Explain the condition:

```javascript
if (body.inflow === undefined && body.outflow === undefined) {
  return errorResponse(
    'Either inflow or outflow is required',
    'VALIDATION_ERROR',
    400
  );
}
```

## Reference Validation

When validating foreign keys:

```javascript
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
```

## Related Notes

- [Error Response Format](/architecture/api/error-response-format.md)
- [Request Validation Pattern](/architecture/api/request-validation-pattern.md)
- [Response Helper Functions](/architecture/api/response-helper-functions.md)
