# Settings Models

Store per-app settings as **supporting** models with a generic shape: an identity primary key
plus a `details` JSONB column. Keep them app-owned, not in a shared identity service.

## Pattern

```javascript
// supporting/OrgSetting.model.js
sequelize.define('OrgSetting', {
  org: {
    type: DataTypes.STRING(12),
    primaryKey: true,
  },
  details: {
    type: DataTypes.JSONB,
    allowNull: false,
    defaultValue: {},
  },
  created_at: { type: DataTypes.DATE, allowNull: false, defaultValue: DataTypes.NOW },
  updated_at: { type: DataTypes.DATE, allowNull: false, defaultValue: DataTypes.NOW },
}, {
  tableName: 'org_settings',
  schema: 'supporting',
  timestamps: false,
});
```

- **`OrgSetting`** — per-org config, PK `org`.
- **`UserSetting`** — per-user config, composite PK `(org, user)`.

Settings are **supporting, not core** — they have no CRUD API resource; they're config
(see the [core-vs-supporting discriminator](/architecture/database/model-organization.md)).

## JSONB-First

Keep individual settings as keys inside `details`, not as bespoke typed columns:

```javascript
// Read: details.consumer_webhook_url — not a top-level column
const url = row?.details?.consumer_webhook_url || null;
```

**Promote a JSONB key to a typed column only when you must filter, join, index, or constrain
on it at the DB level.** A value read by primary-key lookup (the common settings case) never
qualifies — leave it in `details`. This keeps settings models generic and identical across
apps, so they're trivially template-seeded.

## App-Owned, Not Console

With auth-lite, app-level settings live in the app that uses them:

- **Console (identity service)** — identity + genuinely cross-app settings only.
- **Each app** — its own `OrgSetting` / `UserSetting`.

Writing app-specific config into a console-hosted org model creates a synchronous
cross-service dependency that doesn't scale. Keep app settings self-contained.

## Why This Convention?

- **No schema churn** — new settings are JSONB keys, not migrations.
- **Self-contained** — no cross-service read/write for app config.
- **Uniform** — every app's settings models share one generic shape.

## Related Notes
- [Model Organization](/architecture/database/model-organization.md) - Core vs supporting split
- [JSONB Columns](/architecture/database/jsonb-columns.md) - JSONB usage and JSDoc schemas
- [Primary Key Strategies](/architecture/database/primary-key-strategies.md) - Identity PKs
- [Timestamp Columns](/architecture/database/timestamp-columns.md) - `created_at` / `updated_at`
