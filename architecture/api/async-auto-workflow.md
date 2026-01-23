# Async Auto Workflow

Use `async.auto` for complex operations with dependencies.

## Pattern

```javascript
import async from 'async';

const workflow = {
  // Independent task
  getTransactions: async () => {
    return await db.Transactions.findAll({ ... });
  },

  // Dependent task - runs after getTransactions
  populateAccounts: ['getTransactions', async (results) => {
    const transactions = results.getTransactions;
    if (!transactions?.length) return;

    const account_ids = [...new Set(transactions.map(t => t.account))];
    const accounts = await db.Accounts.findAll({
      where: { id: account_ids }
    });

    // Mutate results in place
    const account_map = new Map(accounts.map(a => [a.id, a]));
    transactions.forEach(t => {
      t.account_name = account_map.get(t.account)?.name;
    });
  }],

  // Another independent task (runs in parallel with getTransactions)
  getTotalCount: async () => {
    return await db.Transactions.count({ ... });
  }
};

const results = await async.auto(workflow);
// results.getTransactions - array with account_name populated
// results.getTotalCount - number
```

## Dependency Declaration

```javascript
// No dependencies - runs immediately
taskA: async () => { ... }

// Depends on taskA - waits for taskA to complete
taskB: ['taskA', async (results) => {
  const a_result = results.taskA;
  // ...
}]

// Depends on multiple tasks
taskC: ['taskA', 'taskB', async (results) => {
  const a = results.taskA;
  const b = results.taskB;
  // ...
}]
```

## When to Use

**Good for:**
- Fetching related data (transactions + accounts)
- Operations with clear dependencies
- Parallel execution of independent steps

**Overkill for:**
- Simple sequential operations
- Single database query
- Two unrelated queries (just use Promise.all)

## Error Handling

```javascript
try {
  const results = await async.auto(workflow);
  return successResponse(results.data);
} catch (err) {
  return errorResponse(err.message, 'VALIDATION_ERROR', 400);
}
```

Errors in any task abort the workflow and throw.

## Validation in Workflow

```javascript
const workflow = {
  validateAccount: async () => {
    const account = await db.Accounts.findOne({
      where: { id: body.account, org: auth.org.id }
    });
    if (!account) {
      throw new Error('Account not found');
    }
    return account;
  },

  createTransaction: ['validateAccount', async (results) => {
    // Only runs if validateAccount succeeds
    return await db.Transactions.create({ ... });
  }]
};
```

## Related Notes

- [Async Auto Workflow](/architecture/functional/async-auto-workflow.md)
- [Request Validation Pattern](/architecture/api/request-validation-pattern.md)
- [Next.js Route Organization](/architecture/api/nextjs-route-organization.md)
