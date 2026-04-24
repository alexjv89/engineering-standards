# Database Initialization

Initialize all database models through a centralized registry using a singleton pattern.

## Pattern

Create a `database/index.js` file that initializes all models and returns a database object.

**ESM (preferred for Next.js apps):**
```javascript
import { getSequelizeInstance } from '@/database/sequelize.js';
import AccountsFactory from './models/Accounts.model.js';
import OrgsFactory from './models/Orgs.model.js';
import TransactionsFactory from './models/Transactions.model.js';

let db = null;

function getDB() {
  if (db) {
    return db;
  }

  const sequelize = getSequelizeInstance();

  db = {
    sequelize,
    // Application models
    Accounts: AccountsFactory(sequelize),
    Orgs: OrgsFactory(sequelize),
    Transactions: TransactionsFactory(sequelize),
  };

  return db;
}

export { getDB };
```

**CommonJS (legacy apps):**
```javascript
import { getSequelizeInstance } from '@/database/sequelize.js'

let db = null;

function getDB() {
  if (db) {
    return db;
  }

  const sequelize = getSequelizeInstance();

  db = {
    sequelize,
    // Application models
    Accounts: require('./models/Accounts.model.js')(sequelize),
    Orgs: require('./models/Orgs.model.js')(sequelize),
    Transactions: require('./models/Transactions.model.js')(sequelize),
  };

  return db;
}

export { getDB };
```

## Key Points

- **Singleton pattern**: Cache the `db` object to avoid re-initialization
- **Model registry**: All models accessible from one object
- **Grouping by domain**: Organize models with comments (Application vs Auth)
- **Sequelize access**: Include `sequelize` instance in the db object
- **Lazy loading**: Models initialized only when `getDB()` is first called

## Usage

```javascript
import { getDB } from '@/database';

const db = getDB();
const user = await db.Users.findOne({ where: { email: 'test@example.com' } });
```

## Related Notes
- [Sequelize Initialization](/architecture/database/sequelize-initialization.md) - Creating the Sequelize instance
- [Model Definition Pattern](/architecture/database/model-definition-pattern.md) - Model factory functions
