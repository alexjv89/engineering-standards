# Storybook Mock Data: Centralized Data and Universal Action Mocking

Centralize mock data and use Proxy-based universal action mocking for maintainable stories.

## Mock Directory Structure

```
.storybook/
  ├── mocks/
  │   ├── action.js           # Universal action proxy
  │   └── data/
  │       ├── accounts.json
  │       ├── org.json
  │       ├── members.json
  │       └── statements.json
```

## Universal Action Mocking with Proxy

Use a Proxy to automatically mock any server action:

```javascript
// .storybook/mocks/action.js
const { fn } = require('storybook/test');

const createMockAction = (functionName) => {
  return fn()
    .mockImplementation(async (...args) => {
      console.log(`Mock ${functionName} called with:`, args);
      await new Promise(resolve => setTimeout(resolve, 1000)); // Simulate delay
      return {
        success: true,
        message: `Mock ${functionName} completed successfully`,
      };
    })
    .mockName(functionName);
};

// Proxy pattern for dynamic exports
const mockCache = {};
const handler = {
  get(target, prop) {
    if (typeof prop === 'string') {
      if (!mockCache[prop]) {
        mockCache[prop] = createMockAction(prop);
      }
      return mockCache[prop];
    }
    return undefined;
  }
};

module.exports = new Proxy({}, handler);
```

## Webpack Configuration

Configure webpack to replace action imports:

```javascript
// .storybook/main.js
const path = require('path');
const webpack = require('webpack');

module.exports = {
  // ... other config
  webpackFinal: async (config) => {
    config.plugins.push(
      new webpack.NormalModuleReplacementPlugin(
        /\/action(?:\.js)?$/,
        path.resolve(__dirname, 'mocks/action.js')
      )
    );
    return config;
  }
};
```

## Centralized Mock Data

Store reusable mock data in JSON files:

```json
// .storybook/mocks/data/accounts.json
[
  {
    "id": "acc_1",
    "name": "HDFC Savings Account",
    "type": "bank",
    "category": "savings",
    "bank": "HDFC Bank",
    "number": "****1234"
  },
  {
    "id": "acc_2",
    "name": "ICICI Credit Card",
    "type": "credit_card",
    "bank": "ICICI Bank",
    "number": "****5678"
  }
]
```

## Using Mock Data in Stories

```javascript
import mockAccounts from '../../.storybook/mocks/data/accounts.json';

export const AfterSuccessfulProcessing = {
  args: {
    statement: {
      id: 'stmt-456',
      file_name: 'hdfc_statement_jan_2024.pdf',
      parser_type: 'hdfc_credit_card__pdf',
      account: mockAccounts[1], // Use mock data
      parsed_data: { transactions: [{}, {}] }
    }
  },
}
```

## Inline Mock Data for Edge Cases

For story-specific data, define inline:

```javascript
export const WithUnusualFileName = {
  args: {
    statement: {
      id: 'stmt-123',
      file_name: 'my_very_long_unusual_filename_that_might_break_the_ui_layout.pdf',
      parser_type: 'hdfc_credit__pdf',
      account: { id: 'acc-1', name: 'Test Account' },
    }
  },
}
```

## Mock Server Actions in Stories

Server actions are automatically mocked via the Proxy:

```javascript
// Component uses: import { updateStatement } from './action'
// Storybook automatically provides mock

export const InteractiveTest = {
  args: { statement: mockStatement },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Click update button
    const button = canvas.getByRole('button', { name: /update/i });
    await userEvent.click(button);

    // Action is automatically mocked - no additional setup needed!
    // Check console for: "Mock updateStatement called with: ..."
  },
}
```

## localStorage Mocking for User Types

Use decorators to mock user types:

```javascript
// Staff user stories
export default {
  title: 'Component/StaffUser',
  decorators: [
    (Story) => {
      localStorage.setItem('developer-mode', 'true');
      return <Story />;
    },
  ],
};

// App user stories
export default {
  title: 'Component/AppUser',
  decorators: [
    (Story) => {
      localStorage.removeItem('developer-mode');
      return <Story />;
    },
  ],
};
```

## Benefits of This Pattern

1. **Zero Boilerplate** - Actions auto-mock without manual setup
2. **Consistent Delays** - All actions simulate realistic 1s delay
3. **Debugging Support** - Console logs show all mock calls
4. **Centralized Data** - Mock data reused across stories
5. **Type Safety** - JSON files can be typed
6. **Easy Updates** - Change mock data in one place

## When to Use Each Approach

**Use centralized JSON** when:
- Data is reused across multiple stories
- Data represents common entities (accounts, users, etc.)
- Data should be consistent across stories

**Use inline data** when:
- Testing specific edge cases
- Data is story-specific
- Testing unusual values

## Related Notes
- [Storybook User Journey Pattern](./storybook-user-journey-pattern.md)
- [Storybook Interactions](./storybook-interactions.md)
