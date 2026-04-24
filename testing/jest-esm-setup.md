# Jest Setup for ES Modules Projects

How to configure Jest for projects using native ES modules (`"type": "module"` in package.json).

---

## When This Applies

Use this setup when your project has:

```json
{
  "type": "module"
}
```

in `package.json`. This is common for:
- Node.js workers and services
- CLI tools
- Libraries published as ESM

**Note:** Next.js projects use Babel transforms and don't need this setup — Jest globals work automatically there.

---

## Setup Steps

### 1. Install Jest

```bash
npm install --save-dev jest
```

### 2. Create Config File

Use `.cjs` extension (CommonJS) since the project is ESM:

**jest.config.cjs**
```javascript
/** @type {import('jest').Config} */
module.exports = {
  testEnvironment: 'node',
  transform: {},
  testMatch: ['**/tests/**/*.test.js'],
  moduleFileExtensions: ['js', 'json'],
  clearMocks: true,
  verbose: true,
};
```

**Why `.cjs`?** The project treats `.js` files as ES modules, but Jest's config loader and VS Code's Test Explorer expect CommonJS (`module.exports`). The `.cjs` extension explicitly marks it as CommonJS.

### 3. Add Test Scripts

**package.json**
```json
{
  "scripts": {
    "test": "node --experimental-vm-modules node_modules/jest/bin/jest.js",
    "test:watch": "node --experimental-vm-modules node_modules/jest/bin/jest.js --watch",
    "test:coverage": "node --experimental-vm-modules node_modules/jest/bin/jest.js --coverage"
  }
}
```

The `--experimental-vm-modules` flag enables Jest's native ESM support.

---

## Writing Tests

### Import Jest Globals

In native ESM mode, Jest cannot inject globals. You must explicitly import them:

```javascript
import { jest, describe, it, expect, beforeEach, afterEach } from '@jest/globals';

describe('MyModule', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('should do something', () => {
    expect(true).toBe(true);
  });
});
```

**Common imports:**
- `jest` — for mocking (`jest.fn()`, `jest.mock()`, etc.)
- `describe`, `it`, `test` — test structure
- `expect` — assertions
- `beforeEach`, `afterEach`, `beforeAll`, `afterAll` — lifecycle hooks

### Mocking Modules

Use `jest.unstable_mockModule()` instead of `jest.mock()` for ESM:

```javascript
import { jest } from '@jest/globals';

// Mock BEFORE importing the module that uses it
jest.unstable_mockModule('@fob/lib-worker', () => ({
  txn: {
    apiGet: jest.fn(),
    getTransactions: jest.fn(),
  },
}));

// Import AFTER setting up mocks
const { txn } = await import('@fob/lib-worker');
const { default: myFunction } = await import('../src/myModule.js');
```

**Key differences from CommonJS mocking:**

| CommonJS | ESM |
|----------|-----|
| `jest.mock()` | `jest.unstable_mockModule()` |
| Sync imports | `await import()` after mocking |
| Hoisting works | Must mock before import |

---

## Directory Structure

```
project/
├── jest.config.cjs          # CommonJS config (note .cjs extension)
├── package.json             # "type": "module"
├── src/
│   └── myModule.js
└── tests/
    ├── fixtures/
    │   └── sample-data.js
    └── myModule.test.js
```

---

## Comparison: ESM vs Babel/Next.js

| Aspect | Native ESM | Babel/Next.js |
|--------|-----------|---------------|
| Config file | `jest.config.cjs` | `jest.config.js` |
| Test script | Needs `--experimental-vm-modules` | Standard `jest` |
| Globals | Must import from `@jest/globals` | Auto-injected |
| Mocking | `jest.unstable_mockModule()` + dynamic import | `jest.mock()` with hoisting |
| Transform | None (`transform: {}`) | Babel transform |

---

## Troubleshooting

### "Cannot use import statement outside a module"

Your config file is being parsed as ESM. Rename to `.cjs`:
```bash
mv jest.config.js jest.config.cjs
```

### "describe is not defined"

Add the import at the top of your test file:
```javascript
import { describe, it, expect } from '@jest/globals';
```

### "jest.mock() doesn't work"

In ESM, use `jest.unstable_mockModule()` with dynamic imports:
```javascript
jest.unstable_mockModule('./myModule.js', () => ({
  myFunction: jest.fn(),
}));

const { myFunction } = await import('./myModule.js');
```

### VS Code Test Explorer not detecting tests

1. Ensure config is `.cjs` (CommonJS format)
2. Reload VS Code window
3. Check Jest extension is installed and enabled

---

## Example Test File

```javascript
/**
 * Tests for myService
 */
import { jest, describe, it, expect, beforeEach } from '@jest/globals';

// Mock external dependencies before importing
jest.unstable_mockModule('../src/utils/api.js', () => ({
  fetchData: jest.fn(),
}));

// Dynamic imports after mocking
const { fetchData } = await import('../src/utils/api.js');
const { default: myService } = await import('../src/services/myService.js');

describe('myService', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('should fetch and process data', async () => {
    // Arrange
    fetchData.mockResolvedValue({ items: [1, 2, 3] });

    // Act
    const result = await myService.getData();

    // Assert
    expect(fetchData).toHaveBeenCalledTimes(1);
    expect(result.count).toBe(3);
  });
});
```
