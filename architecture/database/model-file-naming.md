# Model File Naming

Name Sequelize model files using PascalCase model name with `.model.js` suffix.

## Pattern

```
database/models/
├── Users.model.js
├── Transactions.model.js
├── Orgs.model.js
├── Accounts.model.js
├── Api_keys.model.js
└── VerificationTokens.model.js
```

## Naming Convention

**Format**: `{ModelName}.model.js`

- **ModelName**: PascalCase, matching the model name in `sequelize.define()`
- **Suffix**: Always `.model.js` to distinguish from other file types
- **Multi-word names**: Use PascalCase (e.g., `VerificationTokens.model.js`)
- **Underscores in names**: Preserve if needed (e.g., `Api_keys.model.js`)

## Examples

```javascript
// Users.model.js
module.exports = function(sequelize) {
  return sequelize.define('Users', { /* ... */ });
};

// VerificationTokens.model.js
module.exports = function(sequelize) {
  return sequelize.define('VerificationTokens', { /* ... */ });
};
```

## Why This Convention?

- **Easy identification**: `.model.js` clearly indicates Sequelize models
- **Consistency**: File name matches model name in code
- **Alphabetical grouping**: All model files sort together in file explorers
- **No ambiguity**: Clear distinction from services, controllers, etc.

## Related Notes
- [Model Definition Pattern](./model-definition-pattern.md) - How to structure model files
- [Table Naming](./table-naming.md) - How table names differ from model names
