# Storybook Decorator Patterns

Decorators wrap story components to provide layout constraints, context providers, or other environmental setup. This note covers common decorator patterns.

## When to Use Decorators

Use decorators when you need to:

- **Apply layout constraints** (width, height, padding)
- **Provide React context** (theme, state providers)
- **Mock environment** (localStorage, user types)
- **Apply consistent styling** across all stories in a file

## Layout Constraints (Width/Padding)

For components that need specific dimensions:

```javascript
const meta = {
  title: 'components/DatePicker',
  component: DatePicker,
  decorators: [
    (Story) => (
      <div style={{ width: '300px', padding: '20px' }}>
        <Story />
      </div>
    ),
  ],
};
```

**When to use**:
- Components that expand to container width
- Components that need breathing room for dropdowns
- Components better viewed with constraints

**Examples from codebase**:
- src/components/DatePicker.stories.js:26-32
- src/components/MultiSelect.stories.js:26-31

## Full-Height Layout (Tables)

For components that need vertical space:

```javascript
const meta = {
  title: 'components/TransactionsTable',
  component: TransactionsTable,
  parameters: {
    layout: 'fullscreen',
  },
  decorators: [
    (Story) => (
      <div style={{ padding: '20px', height: '100vh' }}>
        <Story />
      </div>
    ),
  ],
};
```

**When to use**:
- Data tables with virtualization
- Full-page layouts
- Components that calculate height dynamically

**Examples from codebase**:
- src/components/TransactionsTable.stories.js:182-188
- src/components/StatementsTable.stories.js:32-38

## Provider Decorators

Wrap components that require React context:

```javascript
import { SnackbarProvider } from './SnackbarProvider';

const meta = {
  title: 'components/ToastButton',
  decorators: [
    (Story) => (
      <SnackbarProvider>
        <Story />
      </SnackbarProvider>
    ),
  ],
};
```

**When to use**:
- Components using context (theme, state)
- Components requiring providers (router, i18n)
- Notification/modal systems

**Example from codebase**:
- src/components/SnackbarProvider.stories.js:128-131

## Query Provider Decorators

For components using React Query:

```javascript
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const query_client = new QueryClient({
  defaultOptions: {
    queries: { retry: false },
  },
});

const meta = {
  title: 'components/DataFetcher',
  decorators: [
    (Story) => (
      <QueryClientProvider client={query_client}>
        <Story />
      </QueryClientProvider>
    ),
  ],
};
```

**When to use**:
- Components that fetch data with React Query
- Components that need query cache

## User Type Mocking

Mock different user types via localStorage:

```javascript
// File: Component.StaffUser.stories.js
const meta = {
  title: 'components/Component/StaffUser',
  decorators: [
    (Story) => {
      localStorage.setItem('developer-mode', 'true');
      return <Story />;
    },
  ],
};
```

```javascript
// File: Component.stories.js (App User)
const meta = {
  title: 'components/Component/AppUser',
  decorators: [
    (Story) => {
      localStorage.removeItem('developer-mode');
      return <Story />;
    },
  ],
};
```

**When to use**:
- Components with different behavior per user type
- Permission-based UI variations

See also: [User Type Mocking](/storybook/user-type-mocking.md) for complete pattern.

## When NOT to Use Decorators

Skip decorators for:

- **Simple components** that work fine without constraints (AlertBox, Pagination)
- **Full-width components** that naturally expand (Page layouts)
- **Cases where parameters.layout is sufficient**

**Examples without decorators**:
- src/components/Table.stories.js (uses parameters only)
- src/components/AlertBox.stories.js (no decorators needed)

## Related Notes
- [Decorator Placement](/storybook/decorator-placement.md) - Where to put decorators
- [Decorator Anti-Patterns](/storybook/decorator-anti-patterns.md) - Common mistakes to avoid
- [Parameters Structure](/storybook/parameters-structure.md) - Alternative to decorators
- [User Type Mocking](/storybook/user-type-mocking.md) - Full user type pattern
- [Meta Object Structure](/storybook/meta-object-structure.md) - Story file configuration
