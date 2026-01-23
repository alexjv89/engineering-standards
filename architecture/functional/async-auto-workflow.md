# async.auto: DAG-Based Workflow Execution

Use `async.auto` for scripts with multiple dependent async operations. It provides declarative dependency management with automatic parallel execution.

## Import

Import only the `auto` function, not the full async library:

```javascript
import auto from 'async/auto';
```

## Key Naming Convention

**Use verb-based keys, not nouns**. The key describes the action, and `results.key` returns the result:

```javascript
// Good - verb keys
const results = await auto({
  getUsers: async () => fetchUsers(),
  getPlants: async () => fetchPlants(),
});
// results.getUsers → users array
// results.getPlants → plants array

// Avoid - noun keys
const results = await auto({
  users: async () => fetchUsers(),    // Unclear what action was taken
  plants: async () => fetchPlants(),
});
```

This convention makes the workflow self-documenting - you can read the keys as a list of actions.

## Basic Pattern

```javascript
const results = await auto({
  // Independent tasks - run in parallel
  loadStatement:    async () => loadStatement(statement_id),
  loadConfig:       async () => loadConfig(),

  // Dependent task - waits for loadStatement
  loadAccount:      ['loadStatement', async (results) =>
    loadAccount(results.loadStatement.account_id)
  ],

  // Multiple dependencies - waits for both
  computeBalances:  ['loadStatement', 'loadAccount', async (results) =>
    compute(results.loadStatement, results.loadAccount)
  ],
});
```

## Inline Implementations

Keep implementations inline within the workflow. The async.auto object itself is foldable in editors, so there's no need to extract short functions:

```javascript
import auto from 'async/auto';

async function processStatement(statement_id) {
  const results = await auto({
    loadStatement: async () => {
      const file_path = `./data/statements/${statement_id}.json`;
      return JSON.parse(await fs.readFile(file_path, 'utf-8'));
    },

    loadAccount: ['loadStatement', async (results) => {
      const file_path = `./data/accounts/${results.loadStatement.account_id}.json`;
      return JSON.parse(await fs.readFile(file_path, 'utf-8'));
    }],

    loadTransactions: ['loadStatement', async (results) => {
      const file_path = `./data/transactions/${results.loadStatement.id}.json`;
      return JSON.parse(await fs.readFile(file_path, 'utf-8'));
    }],

    computeBalances: ['loadTransactions', 'loadAccount', async (results) => {
      const opening = results.loadAccount.balance;
      const total = results.loadTransactions.reduce((sum, t) => sum + t.amount, 0);
      return { opening, closing: opening + total };
    }],

    saveResults: ['computeBalances', async (results) => {
      await fs.writeFile(`./output/${statement_id}.json`, JSON.stringify(results.computeBalances));
      return results.computeBalances;
    }],
  });

  return results.saveResults;
}
```

**When to extract functions:**
- Implementation exceeds ~20 lines
- Logic is reused elsewhere in the codebase
- Complex logic that benefits from unit testing in isolation

## Dependency Syntax

Tasks with dependencies use an array: `[...dependencies, asyncFunction]`

```javascript
await auto({
  // No dependencies - just the function
  getData: async () => fetch('/api/data'),

  // One dependency
  processData: ['getData', async (results) => process(results.getData)],

  // Multiple dependencies
  finalStep: ['getData', 'processData', async (results) =>
    finalize(results.getData, results.processData)
  ],
});
```

## Automatic Parallelization

Tasks run in parallel when their dependencies allow:

```javascript
await auto({
  // These 3 run in parallel (no dependencies)
  loadUsers:    async () => fetchUsers(),
  loadProducts: async () => fetchProducts(),
  loadOrders:   async () => fetchOrders(),

  // This waits for all 3, then runs
  generateReport: ['loadUsers', 'loadProducts', 'loadOrders', async (results) =>
    createReport(results.loadUsers, results.loadProducts, results.loadOrders)
  ],
});
```

## Error Handling

`async.auto` stops execution on first error and rejects the promise:

```javascript
try {
  const results = await auto({
    loadData: async () => {
      throw new Error('Failed to load');
    },
    processData: ['loadData', async (results) =>
      process(results.loadData)  // Never runs
    ],
  });
} catch (error) {
  console.error('Workflow failed:', error.message);
}
```

## When to Use

**Good fit:**
- Scripts with 3+ dependent async operations
- Data pipelines with clear dependencies
- ETL processes
- Complex page data loading

**Not needed:**
- Simple sequential operations (use regular async/await)
- Single async call
- Operations without dependencies

## Type Checking Note

Files using `async.auto` should NOT use `@ts-check` because results are typed as `any`. See [When to Disable @ts-check](/architecture/jsdoc/typecheck/jsdoc-disable-tscheck.md).

```javascript
// Don't add @ts-check - async.auto returns any
import auto from 'async/auto';
```

## Related Notes
- [Pure Functions](/architecture/functional/pure-functions.md)
- [Function Composition](/architecture/functional/function-composition.md)
- [When to Disable @ts-check](/architecture/jsdoc/typecheck/jsdoc-disable-tscheck.md)
