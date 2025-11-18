# Table Naming

Name database tables using lowercase plural with underscores for multi-word names.

## Convention

**Format**: `lowercase_plural`

- **Lowercase only**: No capitals in table names
- **Plural form**: Tables hold multiple records
- **Underscores**: Separate words in multi-word names
- **Always explicit**: Use `tableName` option in model definition

## Examples

```javascript
// Model: Users → Table: users
sequelize.define('Users', { /* ... */ }, {
  tableName: 'users',
});

// Model: Transactions → Table: transactions
sequelize.define('Transactions', { /* ... */ }, {
  tableName: 'transactions',
});

// Model: Api_keys → Table: api_keys
sequelize.define('Api_keys', { /* ... */ }, {
  tableName: 'api_keys',
});

// Model: VerificationTokens → Table: verification_tokens
sequelize.define('VerificationTokens', { /* ... */ }, {
  tableName: 'verification_tokens',
});
```

## Multi-Word Table Names

| Model Name | Table Name | Pattern |
|------------|------------|---------|
| `Users` | `users` | Simple plural |
| `ApiKeys` | `api_keys` | Underscore separator |
| `VerificationTokens` | `verification_tokens` | Underscore separator |
| `EventLogs` | `event_logs` | Underscore separator |

## Schema Prefix

For multi-schema databases, specify the schema:

```javascript
sequelize.define('Users', { /* ... */ }, {
  schema: 'auth',
  tableName: 'users',
});
// Results in: auth.users
```

## Related Notes
- [Model File Naming](./model-file-naming.md) - File naming uses PascalCase
- [Column Naming](./column-naming.md) - Column naming conventions
