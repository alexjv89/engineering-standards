# Timestamp Columns

Manually define timestamp columns using `snake_case` naming and disable Sequelize's automatic timestamp management.

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
    allowNull: true,
    defaultValue: DataTypes.NOW,
  },
  // ... other fields
}, {
  tableName: 'transactions',
  timestamps: false,  // Disable Sequelize auto-timestamps
});
```

## Key Points

- **Naming**: Use `snake_case` (`created_at`, `updated_at`)
- **Disable auto-timestamps**: Set `timestamps: false` in options
- **Explicit definition**: Define timestamp columns manually
- **Default value**: Use `DataTypes.NOW` for automatic timestamps
- **Nullable**: `created_at` usually NOT NULL, `updated_at` can be nullable

## Common Timestamp Columns

```javascript
created_at: {
  type: DataTypes.DATE,
  allowNull: false,
  defaultValue: DataTypes.NOW,
}

updated_at: {
  type: DataTypes.DATE,
  allowNull: true,
  defaultValue: DataTypes.NOW,
}

expires_at: {
  type: DataTypes.DATE,
  allowNull: true,
}
```

## Why Disable Auto-Timestamps?

Sequelize's default `timestamps: true` creates `createdAt` and `updatedAt` in `camelCase`, which:
- Doesn't match our `snake_case` convention
- Is inconsistent with other column names
- Creates naming confusion in the codebase

**Avoid** the Sequelize default:
```javascript
// Sequelize default (DON'T USE)
sequelize.define('Model', { /* ... */ }, {
  timestamps: true,  // Creates createdAt, updatedAt (camelCase)
});
```

## Updating `updated_at` Manually

Since auto-timestamps are disabled, update `updated_at` explicitly:

```javascript
await db.Orgs.update({
  name: 'New Name',
  updated_at: new Date(),
}, {
  where: { id: org_id }
});
```

## Related Notes
- [Column Naming](./column-naming.md) - All columns use snake_case
- [Model Definition Pattern](./model-definition-pattern.md) - Model structure
