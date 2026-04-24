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

## Alternative: Sequelize auto-timestamps with mapping

An alternative to explicit column definitions is to use Sequelize's auto-timestamps with
column name mapping. The result is identical — `created_at` and `updated_at` in the DB:

```javascript
sequelize.define('Orgs', { /* fields */ }, {
  tableName: 'orgs',
  timestamps: true,
  createdAt: 'created_at',
  updatedAt: 'updated_at',
});
```

This can also be set globally in the Sequelize instance (see [Sequelize Initialization](/architecture/database/sequelize-initialization.md)),
avoiding repetition across every model. Either approach is acceptable as long as it's consistent
within the app.

**Avoid** the Sequelize default with no mapping:
```javascript
// Creates camelCase columns (DON'T USE without mapping)
sequelize.define('Model', { /* ... */ }, {
  timestamps: true,  // Creates createdAt, updatedAt columns — wrong naming
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
- [Column Naming](/architecture/database/column-naming.md) - All columns use snake_case
- [Model Definition Pattern](/architecture/database/model-definition-pattern.md) - Model structure
