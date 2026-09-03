# Required Columns

Every core model has `id`, `org`, `created_at`, and `updated_at`. Supporting models always have `created_at`, and `updated_at` whenever the row can change.

## The set

| Column | Core models | Supporting models | Why |
|--------|-------------|-------------------|-----|
| `id` | Required | Required (join tables may use a composite key) | [Primary Key Strategies](/architecture/database/primary-key-strategies.md) |
| `org` | Required | Required, except pure join tables | Tenant isolation — every query is org-scoped ([Foreign Key Pattern](/architecture/database/foreign-key-pattern.md)) |
| `created_at` | Required | Required | When the row appeared |
| `updated_at` | Required | Required if the row is mutable (`api_keys`, `org_settings`, `user_settings`) | When the row last changed — what a sync consumer or an audit needs |

```javascript
sequelize.define('Transactions', {
  id:         { type: DataTypes.STRING(12), primaryKey: true },
  org:        { type: DataTypes.STRING(12), allowNull: false },
  created_at: { type: DataTypes.DATE, allowNull: false, defaultValue: DataTypes.NOW },
  updated_at: { type: DataTypes.DATE, allowNull: false, defaultValue: DataTypes.NOW },
  // ... domain fields
}, { schema: 'core', tableName: 'transactions', timestamps: false });
```

## Exceptions

- **Append-only tables** (`event_logs`, a ledger's `entries`) omit `updated_at` and say so in a comment on the model — the row is never updated, so the column would only ever equal `created_at`.
- **Domain-modelled lifecycles** may replace the pair with more specific columns (`started_at` / `last_seen_at` / `ended_at` on a session table). Name the choice in the model; don't just leave the columns out.

A column that exists in `db-setup.sql` but not in the model is not "present" — the ORM can neither read nor filter on it.

## Related Notes

- [Timestamp Columns](/architecture/database/timestamp-columns.md) — how `created_at` / `updated_at` are declared
- [Updated-at Trigger](/architecture/database/updated-at-trigger.md) — how `updated_at` is kept true
- [Primary Key Strategies](/architecture/database/primary-key-strategies.md) — choosing the `id` type
- [Foreign Key Pattern](/architecture/database/foreign-key-pattern.md) — `org` and other reference columns
- [Supporting models (platform-handbook)](https://github.com/finopsbricks/platform-handbook/blob/main/data-models/supporting.md) — the org-isolation mandate
