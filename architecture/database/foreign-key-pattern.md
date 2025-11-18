# Foreign Key Pattern

Define foreign keys manually without Sequelize associations, using singular table names as column names.

## Standard Pattern

```javascript
sequelize.define('Transactions', {
  account: {
    type: DataTypes.STRING(12),
    allowNull: true,
    // references: {
    //   model: 'accounts',
    //   key: 'id',
    // },
  },
  org: {
    type: DataTypes.STRING(12),
    allowNull: true,
  },
  statement: {
    type: DataTypes.TEXT,
    allowNull: true,
    references: {
      model: 'statements',
      key: 'id',
    },
  },
});
```

## Naming Convention

**Rule**: Foreign key column name matches the **singular** form of the referenced table

| Referenced Table | FK Column Name | Type |
|------------------|----------------|------|
| `accounts` | `account` | STRING(12) |
| `orgs` | `org` | STRING(12) |
| `statements` | `statement` | TEXT |
| `users` | `user` | TEXT |

## Manual vs Sequelize Associations

**Current approach**: Manual foreign keys (commented out references)

```javascript
account: {
  type: DataTypes.STRING(12),
  allowNull: true,
  // references: {          // Usually commented out
  //   model: 'accounts',
  //   key: 'id',
  // },
},
```

**Why manual?**
- **Flexibility**: No automatic relationship behavior
- **Simpler queries**: Explicit control over joins
- **No magic**: Clear what queries are executed
- **Avoid complexity**: Sequelize associations can be complex

## When to Use `references`

Include `references` for:
- **Database-level constraints**: Enforce referential integrity
- **Documentation**: Make relationships explicit in schema
- **Migration tools**: Help migration generators understand relationships

```javascript
statement: {
  type: DataTypes.TEXT,
  allowNull: true,
  references: {
    model: 'statements',  // Referenced table name
    key: 'id',            // Referenced column
  },
},
```

## Querying with Manual FKs

```javascript
// Without associations, use manual joins
const db = getDB();
const transaction = await db.Transactions.findOne({
  where: { id: txn_id },
  raw: true,
});

// Then fetch related record separately
const account = await db.Accounts.findOne({
  where: { id: transaction.account },
  raw: true,
});
```

## Type Matching

Ensure FK column type matches referenced primary key type:

```javascript
// Accounts.id is STRING(12)
// So foreign key should also be STRING(12)
account: {
  type: DataTypes.STRING(12),  // Match PK type
  allowNull: true,
}

// Users.id is TEXT (UUID)
// So foreign key should be TEXT
user: {
  type: DataTypes.TEXT,  // Match PK type
  allowNull: true,
}
```

## Related Notes
- [Column Naming](./column-naming.md) - All columns use snake_case
- [Primary Key Strategies](./primary-key-strategies.md) - Types of primary keys
- [Table Naming](./table-naming.md) - Table names are plural
