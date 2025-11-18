# Database Patterns

Atomic notes on Sequelize ORM patterns, model definitions, and database conventions.

## Initialization & Setup
- [Sequelize Initialization](./sequelize-initialization.md) - Singleton instance pattern with environment configuration
- [Database Initialization](./database-initialization.md) - Model registry and database setup
- [Model Definition Pattern](./model-definition-pattern.md) - Factory function pattern for defining models

## Naming Conventions
- [Model File Naming](./model-file-naming.md) - PascalCase.model.js convention
- [Table Naming](./table-naming.md) - Lowercase plural with underscores
- [Column Naming](./column-naming.md) - snake_case standard with exceptions

## Column Patterns
- [Primary Key Strategies](./primary-key-strategies.md) - STRING(12), UUID, or auto-increment
- [Timestamp Columns](./timestamp-columns.md) - created_at/updated_at patterns
- [JSONB Columns](./jsonb-columns.md) - Flexible data with JSDoc documentation
- [Foreign Key Pattern](./foreign-key-pattern.md) - Manual FK definitions and naming

## Advanced Patterns
- [Custom Model Methods](./custom-model-methods.md) - Adding static methods to models
