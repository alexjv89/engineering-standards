# Model Definition Pattern

Define Sequelize models as factory functions that accept a Sequelize instance and return the defined model.

## Pattern

Each model file exports a function that defines the model:

```javascript
const { DataTypes } = require('sequelize');

module.exports = function(sequelize) {
  return sequelize.define('Users', {
    id: {
      type: DataTypes.TEXT,
      primaryKey: true,
      defaultValue: () => crypto.randomUUID(),
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
};
```

## Key Points

- **Factory function pattern**: Export a function, not the model directly
- **Sequelize instance parameter**: Function receives `sequelize` as argument
- **Model name**: First argument to `define()` is the model name (PascalCase)
- **Attributes**: Second argument defines column schema
- **Options**: Third argument for table name, timestamps, schema, etc.
- **CommonJS**: Use `require()` and `module.exports` for model files

## Why This Pattern?

- **Lazy initialization**: Models only created when needed
- **Dependency injection**: Sequelize instance passed in
- **Testability**: Easy to mock the sequelize instance
- **Single responsibility**: Each file defines one model

## Related Notes
- [Sequelize Initialization](./sequelize-initialization.md) - Creating the Sequelize instance
- [Database Initialization](./database-initialization.md) - Calling these factory functions
- [Model File Naming](./model-file-naming.md) - How to name model files
- [Table Naming](./table-naming.md) - tableName option convention
