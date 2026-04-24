# Model Definition Pattern

Define Sequelize models as factory functions that accept a Sequelize instance and return the defined model.

## Pattern

Each model file exports a factory function that accepts a Sequelize instance and returns the defined model. Both ESM and CommonJS are acceptable — use whichever is consistent with the rest of the app.

**ESM (preferred for Next.js apps):**
```javascript
import { DataTypes } from 'sequelize';

export default function(sequelize) {
  return sequelize.define('Users', {
    id: {
      type: DataTypes.TEXT,
      primaryKey: true,
    },
    name: {
      type: DataTypes.TEXT,
      allowNull: true,
    },
    email: {
      type: DataTypes.TEXT,
      unique: true,
    },
  }, {
    schema: 'auth',
    tableName: 'users',
    timestamps: false,
  });
}
```

**CommonJS (legacy apps):**
```javascript
const { DataTypes } = require('sequelize');

module.exports = function(sequelize) {
  return sequelize.define('Users', { /* ... */ }, {
    schema: 'auth',
    tableName: 'users',
    timestamps: false,
  });
};
```

## Key Points

- **Factory function pattern**: Export a function, not the model directly
- **Sequelize instance parameter**: Function receives `sequelize` as argument
- **Model name**: First argument to `define()` is the model name (PascalCase)
- **Attributes**: Second argument defines column schema
- **Options**: Third argument for table name, timestamps, schema, etc.
- **Module system**: ESM or CommonJS — be consistent within the app. Next.js handles interop transparently. Sequelize 6 works with both via Next.js bundling.

## Why This Pattern?

- **Lazy initialization**: Models only created when needed
- **Dependency injection**: Sequelize instance passed in
- **Testability**: Easy to mock the sequelize instance
- **Single responsibility**: Each file defines one model

## Related Notes
- [Sequelize Initialization](/architecture/database/sequelize-initialization.md) - Creating the Sequelize instance
- [Database Initialization](/architecture/database/database-initialization.md) - Calling these factory functions
- [Model File Naming](/architecture/database/model-file-naming.md) - How to name model files
- [Table Naming](/architecture/database/table-naming.md) - tableName option convention
