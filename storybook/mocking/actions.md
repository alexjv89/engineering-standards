# Storybook: Universal Action Mocking

Auto-mock server actions using a Proxy pattern for zero-boilerplate story setup.

## Universal Action Proxy

Automatically mock any server action without manual setup:

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

## Usage in Stories

Actions are automatically mocked - no additional setup needed:

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

    // Action is automatically mocked!
    // Check console for: "Mock updateStatement called with: ..."
  },
}
```

## Benefits

1. **Zero Boilerplate** - Actions auto-mock without manual setup
2. **Consistent Delays** - All actions simulate realistic 1s delay
3. **Debugging Support** - Console logs show all mock calls
4. **Named Mocks** - mockName() provides clear error messages

## Related Notes
- [Mock Data Organization](/storybook/mocking/data.md)
- [User Type Mocking](/storybook/mocking/user-types.md)
- [Play Functions Basics](/storybook/play-functions/basics.md)
