# Timestamp Columns

Declare `created_at` and `updated_at` explicitly as `snake_case`, `NOT NULL DEFAULT now()`, `TIMESTAMPTZ` columns with `timestamps: false`; the database keeps `updated_at` current, application code never sets it.

## Standard Pattern

```javascript
sequelize.define('Transactions', {
  created_at: {
    type: DataTypes.DATE,
    allowNull: false,
    defaultValue: DataTypes.NOW,
  },
  updated_at: {
    type: DataTypes.DATE,
    allowNull: false,
    defaultValue: DataTypes.NOW,
  },
  // ... other fields
}, {
  tableName: 'transactions',
  timestamps: false,  // Sequelize does not manage these
});
```

```sql
created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
```

## Key Points

- **Naming**: `snake_case`, `{action}_at` — `created_at`, `updated_at`, `expires_at`
- **Explicit**: define the attributes in the model; `timestamps: false`
- **Never null**: both default to `now()`. A row that has never changed has `updated_at = created_at`, not `NULL`, so consumers can always sort and filter on it
- **`TIMESTAMPTZ`** for new columns. Zoneless `TIMESTAMP` silently takes the session timezone on `now()`; existing columns are converted in their own migration
- **`updated_at` is maintained by a trigger** — see [Updated-at Trigger](/architecture/database/updated-at-trigger.md). Do not write `updated_at: new Date()` in application code, and do not enable Sequelize's `timestamps: true` (even with `createdAt`/`updatedAt` mapping): two mechanisms for one fact is how the column drifts, and neither ORM path covers raw SQL or backfills

## Avoid

```javascript
// Wrong column names — Sequelize defaults create createdAt / updatedAt
sequelize.define('Model', { /* ... */ }, { timestamps: true });

// Second mechanism — the trigger already does this, and this misses every non-ORM write
await db.Orgs.update({ name, updated_at: new Date() }, { where: { id } });
```

## Related Notes
- [Required Columns](/architecture/database/required-columns.md) — which models must carry these
- [Updated-at Trigger](/architecture/database/updated-at-trigger.md) — the maintenance mechanism
- [Column Naming](/architecture/database/column-naming.md) — all columns use snake_case
- [Model Definition Pattern](/architecture/database/model-definition-pattern.md) — model structure
- [Date Range Filtering](/architecture/api/date-range-filtering.md) — filtering on these columns over the API
