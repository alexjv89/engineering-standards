# Response Helper Functions

Centralize response creation in utility functions.

## Core Functions

```javascript
// src/utils/api/auth.js

import { NextResponse } from 'next/server';

/**
 * Success response for single item
 * @param {Object} data - Response data
 * @param {number} [status=200] - HTTP status
 */
export function successResponse(data, status = 200) {
  return NextResponse.json({ data }, { status });
}

/**
 * List response with pagination
 * @param {Array} items - Array of items
 * @param {Object} page_context - Pagination info
 */
export function listResponse(items, page_context) {
  return NextResponse.json({
    data: items,
    page_context: {
      count: items.length,
      page: page_context.page,
      per_page: page_context.per_page,
      total: page_context.total,
      has_more: page_context.page * page_context.per_page < page_context.total
    }
  });
}

/**
 * Error response
 * @param {string} message - Error message
 * @param {string} [code='BAD_REQUEST'] - Error code
 * @param {number} [status=400] - HTTP status
 */
export function errorResponse(message, code = 'BAD_REQUEST', status = 400) {
  return NextResponse.json({ error: { code, message } }, { status });
}

/**
 * Not found response
 * @param {string} resource - Resource name
 */
export function notFoundResponse(resource) {
  return NextResponse.json(
    { error: { code: 'NOT_FOUND', message: `${resource} not found` } },
    { status: 404 }
  );
}
```

## Usage

```javascript
import {
  successResponse,
  listResponse,
  errorResponse,
  notFoundResponse
} from '@/utils/api/auth';

// Single item
return successResponse(transaction);

// Created item
return successResponse(transaction, 201);

// List with pagination
return listResponse(transactions, { page, per_page: limit, total });

// Validation error
return errorResponse('date is required', 'VALIDATION_ERROR', 400);

// Not found
return notFoundResponse('Transaction');

// Delete success (no helper needed)
return new NextResponse(null, { status: 204 });
```

## Why Helpers?

- **Consistency**: Same envelope structure everywhere
- **DRY**: Don't repeat response wrapping logic
- **Maintainability**: Change format in one place
- **Readability**: Intent clear from function name

## Related Notes

- [Response Envelope Pattern](/architecture/api/response-envelope.md)
- [Error Response Format](/architecture/api/error-response-format.md)
- [Pagination Pattern](/architecture/api/pagination-pattern.md)
