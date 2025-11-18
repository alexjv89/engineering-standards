# Custom Model Methods

Add custom static methods to Sequelize models for domain-specific query logic.

## Pattern

Define the model, add custom static methods, then return the model:

```javascript
const { DataTypes, Op } = require('sequelize');

module.exports = function(sequelize) {
  const Orgs = sequelize.define('Orgs', {
    id: {
      type: DataTypes.STRING(12),
      primaryKey: true,
    },
    slug: {
      type: DataTypes.TEXT,
      allowNull: true,
      unique: true,
    },
    name: {
      type: DataTypes.TEXT,
      allowNull: true,
    },
  }, {
    tableName: 'orgs',
    timestamps: false,
  });

  // Add custom static method
  Orgs.findByIdOrSlug = async function(id_or_slug, options = {}) {
    return await this.findOne({
      where: {
        [Op.or]: [
          { id: id_or_slug },
          { slug: id_or_slug }
        ]
      },
      raw: true,
      ...options
    });
  };

  return Orgs;
};
```

## Usage

```javascript
const db = getDB();

// Use custom method
const org = await db.Orgs.findByIdOrSlug('acme-corp');
// Finds by either ID or slug
```

## When to Add Custom Methods

- **Complex queries**: Reusable query logic used in multiple places
- **Domain logic**: Business-specific finding/filtering operations
- **Multiple conditions**: OR/AND combinations that repeat
- **Convenience**: Simplify common access patterns

## Naming Convention

Use descriptive method names following these patterns:

- `findBy{Criteria}`: Finding records by specific criteria
- `getAll{Subset}`: Getting filtered collections
- `search{Entity}`: Search operations
- Use `snake_case` for method names (consistent with variables)

```javascript
// Good examples
Orgs.find_by_id_or_slug
Users.find_by_email_or_username
Transactions.get_all_for_account
```

## Best Practices

**1. Return consistent formats**
```javascript
// Use raw: true for consistency
Orgs.findByIdOrSlug = async function(id_or_slug, options = {}) {
  return await this.findOne({
    where: { /* ... */ },
    raw: true,  // Return plain objects, not instances
    ...options
  });
};
```

**2. Allow option overrides**
```javascript
// Accept options parameter for flexibility
async function(param, options = {}) {
  return await this.findOne({
    // ... default options
    ...options  // Allow caller to override
  });
}
```

**3. Use async/await**
```javascript
// Always use async/await for query methods
Orgs.customMethod = async function() {
  return await this.findOne({ /* ... */ });
};
```

## Static vs Instance Methods

**Static methods** (on the Model itself):
```javascript
Orgs.findBySlug = async function(slug) { /* ... */ }
// Usage: await db.Orgs.findBySlug('acme')
```

**Instance methods** (on model instances):
```javascript
Orgs.prototype.archive = async function() {
  this.is_archived = true;
  return await this.save();
}
// Usage: await org_instance.archive()
```

**Prefer static methods** for:
- Finding/querying records
- Bulk operations
- Factory methods

**Use instance methods** for:
- Operations on a single record
- State transitions
- Computed properties

## Related Notes
- [Model Definition Pattern](./model-definition-pattern.md) - Basic model structure
- [Functions: snake_case](../../naming/functions-snake-case.md) - Function naming convention
