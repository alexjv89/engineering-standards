# API: Validation Returns All Errors

## Current
Validation returns first error encountered, then stops.

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "date is required"
  }
}
```

## Proposed
Return all validation errors at once.

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Multiple validation errors",
    "details": {
      "fields": {
        "date": "date is required",
        "account": "account is required"
      }
    }
  }
}
```

## Why
- Better UX: user fixes all issues in one pass
- Reduces round-trips during form submission

## Complexity
- Requires collecting errors instead of early return
- Need to decide on `details` structure

## Files to Update
- `/architecture/api/validation-error-pattern.md`
- `/architecture/api/request-validation-pattern.md`
- `/architecture/api/error-response-format.md`
