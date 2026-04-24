# Sequelize Initialization

Use a singleton pattern to initialize the Sequelize instance, ensuring only one database connection is created across the application.

## Pattern

Create a `sequelize.js` file with a factory function that returns a cached Sequelize instance:

```javascript
import { Sequelize } from 'sequelize';

let sequelize = null;

function getSequelizeInstance() {
  if (sequelize) {
    return sequelize;
  }

  sequelize = new Sequelize(process.env.DB_APP, {
    logging: false,
    dialect: 'postgres',
    dialectOptions: process.env.NODE_ENV === 'production' ? {
      ssl: {
        require: true,
        rejectUnauthorized: false
      }
    } : {}
  });

  return sequelize;
}

export { getSequelizeInstance };
```

## Optional: Global `define` for timestamp mapping

If all models in the app share the same timestamp column naming, you can set it once globally
instead of repeating it in every model definition:

```javascript
sequelize = new Sequelize(process.env.DB_APP, {
  logging: false,
  dialect: 'postgres',
  define: {
    timestamps: true,
    createdAt: 'created_at',
    updatedAt: 'updated_at',
  },
  dialectOptions: { /* ... */ }
});
```

**Caution — avoid `underscored: true`**: This flag silently converts all camelCase JS property
names to snake_case DB columns. It adds hidden behavior that can conflict with explicit `field`
mappings (e.g. in BetterAuth models) and makes column naming less obvious when reading model files.
Prefer explicit snake_case property names in model definitions instead.

## Key Points

- **Singleton pattern**: Check if instance exists before creating
- **Environment-based config**: Connection string from `process.env.DB_APP`
- **SSL in production**: Enable SSL for production PostgreSQL connections
- **Logging disabled**: Set `logging: false` to reduce console noise
- **Export function, not instance**: Allows lazy initialization
- **Global define**: Optional DRY shortcut for shared timestamp mapping — valid alternative to per-model options

## Related Notes
- [Database Initialization](/architecture/database/database-initialization.md) - Using Sequelize instance to initialize models
- [Model Definition Pattern](/architecture/database/model-definition-pattern.md) - How models use this instance
