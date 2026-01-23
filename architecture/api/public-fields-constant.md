# Public Fields Constant

Define allowed API fields as a constant at the top of route files.

## Pattern

```javascript
const PUBLIC_FIELDS = [
  'id',
  'date',
  'particulars',
  'inflow',
  'outflow',
  'balance',
  'entity',
  'category',
  'type',
  'account',        // DB field name (the ID)
  'account_name',   // Suffixed expansion
  'statement'
];
```

## Placement

At the top of the route file, after imports:

```javascript
import { getDB } from "@/database";
import { authenticateRequest, listResponse } from '@/utils/api/auth';

const PUBLIC_FIELDS = ['id', 'name', 'category', 'description', 'created_at'];
const VALID_CATEGORIES = ['bank', 'credit_card'];

export async function GET(request) {
  // ...
}
```

## Usage

Pass to field selection utilities:

```javascript
const selected_fields = parseFieldSelection(request, PUBLIC_FIELDS);
```

## DB Fields for Queries

Define DB fields separately when you need to limit query columns:

```javascript
const PUBLIC_FIELDS = ['id', 'date', 'account', 'account_name'];
const DB_FIELDS = ['id', 'date', 'account', 'org'];  // Includes internal fields for logic

const transactions = await db.Transactions.findAll({
  attributes: DB_FIELDS,  // Query only needed columns
  // ...
});
```

Note: `account` appears in both - no renaming needed.

## Why Constants?

- **Documentation**: Clear what fields are exposed
- **Validation**: Used by `parseFieldSelection()` to filter invalid requests
- **Consistency**: Same list used for all operations on resource
- **Security**: Explicit allowlist prevents accidental exposure

## Per-Resource Fields

Each resource defines its own PUBLIC_FIELDS:

**Transactions:**
```javascript
const PUBLIC_FIELDS = ['id', 'date', 'particulars', 'inflow', 'outflow', 'balance', 'entity', 'category', 'type', 'account', 'account_name', 'statement'];
```

**Accounts:**
```javascript
const PUBLIC_FIELDS = ['id', 'name', 'category', 'description', 'acc_no', 'currency', 'color', 'opening_balance', 'is_archived', 'upload_frequency', 'created_at'];
```

**Rules:**
```javascript
const PUBLIC_FIELDS = ['id', 'name', 'description', 'priority', 'conditions', 'actions', 'enabled', 'created_at', 'updated_at'];
```

## Related Notes

- [Field Selection](/architecture/api/field-selection.md)
- [Field Selection Utilities](/architecture/api/field-selection-utilities.md)
- [Format Entity Pattern](/architecture/api/format-entity-pattern.md)
