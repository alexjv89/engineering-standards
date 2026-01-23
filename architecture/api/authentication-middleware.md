# Authentication Middleware

Use a single function to authenticate and authorize requests.

## Pattern

```javascript
export async function authenticateRequest(request, model, action) {
  const auth = await validateApiKey(request);

  if ('error' in auth) {
    return { response: authError(auth.error, auth.status) };
  }

  if (!hasPermission(auth.api_key, model, action)) {
    return { response: permissionError(model, action) };
  }

  return { auth };
}
```

## Return Type

Returns either:
- `{ auth }` - Authentication successful
- `{ response }` - Error response to return immediately

## Usage in Route Handlers

```javascript
export async function GET(request) {
  const { auth, response } = await authenticateRequest(request, 'transactions', 'read');
  if (response) return response;

  // auth.org - Organization record
  // auth.api_key - API key record with scopes
  const transactions = await db.Transactions.findAll({
    where: { org: auth.org.id }
  });

  return listResponse(transactions, { ... });
}

export async function POST(request) {
  const { auth, response } = await authenticateRequest(request, 'transactions', 'create');
  if (response) return response;

  // Create transaction scoped to org
  const transaction = await db.Transactions.create({
    org: auth.org.id,
    ...body
  });

  return successResponse(transaction, 201);
}
```

## Validate API Key

```javascript
export async function validateApiKey(request) {
  const api_key = request.headers.get('api-key');
  const api_secret = request.headers.get('api-secret');

  if (!api_key || !api_secret) {
    return { error: 'Missing API credentials', status: 401 };
  }

  const key_record = await db.Api_keys.findOne({
    where: { key: api_key, secret: api_secret }
  });

  if (!key_record) {
    return { error: 'Invalid API credentials', status: 401 };
  }

  if (key_record.expires_at && new Date(key_record.expires_at) < new Date()) {
    return { error: 'API key expired', status: 401 };
  }

  const org = await db.Orgs.findOne({ where: { id: key_record.org } });

  return { org, api_key: key_record };
}
```

## Early Return Pattern

The tuple return enables clean early returns:

```javascript
const { auth, response } = await authenticateRequest(request, model, action);
if (response) return response;  // Early exit if auth failed

// Continue with authenticated request
```

## Related Notes

- [Permission Scopes](/architecture/api/permission-scopes.md)
- [Error Response Format](/architecture/api/error-response-format.md)
- [Next.js Route Organization](/architecture/api/nextjs-route-organization.md)
