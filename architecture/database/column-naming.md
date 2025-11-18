# Column Naming

Use `snake_case` for database column names as the standard convention.

## Convention

**Standard**: `snake_case` for all column names

```javascript
sequelize.define('Transactions', {
  created_at: DataTypes.DATE,
  raw_data: DataTypes.JSONB,
  opening_balance: DataTypes.INTEGER,
  file_name: DataTypes.TEXT,
  acc_no: DataTypes.TEXT,
});
```

## Common Patterns

| Column Purpose | Naming Pattern | Example |
|----------------|----------------|---------|
| Timestamps | `{action}_at` | `created_at`, `updated_at`, `expires_at` |
| Booleans | `is_{state}` or `to_be_{action}` | `is_archived`, `to_be_deleted` |
| Foreign Keys | `{table_name}` (singular) | `org`, `account`, `user` |
| Data fields | `{descriptor}_{type}` | `raw_data`, `parsed_data`, `file_name` |

## Inconsistency Note

**Exception**: Some models use `camelCase` for Sequelize auto-timestamp fields:

```javascript
// Non-standard (avoid in new models)
sequelize.define('Members', {
  createdAt: DataTypes.DATE,  // camelCase
  updatedAt: DataTypes.DATE,  // camelCase
});
```

**Recommendation**: Use `snake_case` with `timestamps: false` for consistency:

```javascript
// Preferred approach
sequelize.define('Members', {
  created_at: DataTypes.DATE,  // snake_case
  updated_at: DataTypes.DATE,  // snake_case
}, {
  timestamps: false,  // Disable Sequelize auto-timestamps
});
```

## Boolean Prefixes

```javascript
is_archived: DataTypes.BOOLEAN,      // State
is_verified: DataTypes.BOOLEAN,      // State
to_be_deleted: DataTypes.BOOLEAN,    // Pending action
```

## Related Notes
- [Table Naming](./table-naming.md) - Table naming uses lowercase plural
- [Timestamp Columns](./timestamp-columns.md) - Standard timestamp patterns
- [Foreign Key Pattern](./foreign-key-pattern.md) - FK naming conventions
- [Variables: snake_case](../../naming/variables-snake-case.md) - JavaScript variable naming
