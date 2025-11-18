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

## Key Points

- **Singleton pattern**: Check if instance exists before creating
- **Environment-based config**: Connection string from `process.env.DB_APP`
- **SSL in production**: Enable SSL for production PostgreSQL connections
- **Logging disabled**: Set `logging: false` to reduce console noise
- **Export function, not instance**: Allows lazy initialization

## Related Notes
- [Database Initialization](./database-initialization.md) - Using Sequelize instance to initialize models
- [Model Definition Pattern](./model-definition-pattern.md) - How models use this instance
