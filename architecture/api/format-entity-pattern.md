# Format Entity Pattern

Transform database records to API response shape.

## Pattern

Define a format function per resource:

```javascript
/**
 * Format transaction for API response
 * @param {Object} transaction - Raw transaction from DB
 * @param {Object} account - Related account
 * @returns {Object}
 */
function formatTransaction(transaction, account) {
  return {
    id: transaction.id,
    date: transaction.date,
    particulars: transaction.particulars,
    inflow: transaction.inflow,
    outflow: transaction.outflow,
    balance: transaction.balance,
    entity: transaction.entity,
    category: transaction.category,
    type: transaction.type,
    account: transaction.account,        // Keep DB field name (this is the ID)
    account_name: account?.name,         // Add suffixed expansion
    statement: transaction.statement
  };
}
```

## What Format Functions Do

1. **Keep DB field names**: `account` stays `account` (it's the ID)
2. **Add suffixed expansions**: `account_name` for related data
3. **Hide internal fields**: Remove `org`, `fingerprint`, `created_at`
4. **Transform values**: Convert units, format dates
5. **Ensure shape**: Explicit property list prevents accidental exposure

## Why Keep DB Field Names?

- **Simplicity**: No mapping to remember between DB and API
- **Less code**: No renaming logic in format functions
- **Consistency**: Field names match across layers
- **Convention**: `account` is the ID, `account_name` is the expansion

## Usage

```javascript
export async function GET(request, { params }) {
  const { id } = await params;

  const transaction = await db.Transactions.findOne({
    where: { id, org: auth.org.id },
    raw: true
  });

  if (!transaction) {
    return notFoundResponse('Transaction');
  }

  const account = await db.Accounts.findOne({
    where: { id: transaction.account }
  });

  const formatted = formatTransaction(transaction, account);
  return successResponse(formatted);
}
```

## For Lists

```javascript
const transactions = await db.Transactions.findAll({ ... });

const formatted = transactions.map(t => {
  const account = account_map.get(t.account);
  return formatTransaction(t, account);
});

return listResponse(formatted, { ... });
```

## Value Transformations

```javascript
function formatAccount(account) {
  return {
    id: account.id,
    name: account.name,
    // Convert from milli-units to display units
    opening_balance: account.opening_balance
      ? account.opening_balance / 1000
      : null,
    // ...
  };
}
```

## Why Not Return Raw?

- **Security**: Don't expose internal fields (`org`, `fingerprint`)
- **Stability**: API shape independent of DB schema
- **Clarity**: Explicit about what's public
- **Relations**: Flatten related data cleanly

## Related Notes

- [Public Fields Constant](/architecture/api/public-fields-constant.md)
- [Field Selection Utilities](/architecture/api/field-selection-utilities.md)
- [Response Envelope Pattern](/architecture/api/response-envelope.md)
