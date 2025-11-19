# Storybook Decorator Anti-Patterns

Common mistakes to avoid when using decorators, with examples of what to do instead.

## Anti-Pattern 1: Overly Complex Inline Styles

```javascript
// BAD: Overly complex inline styles
decorators: [
  (Story) => (
    <div style={{
      width: '600px',
      height: '400px',
      padding: '20px',
      margin: '0 auto',
      border: '1px solid #ccc',
      borderRadius: '8px',
      boxShadow: '0 2px 8px rgba(0,0,0,0.1)',
      background: 'linear-gradient(to bottom, #fff, #f5f5f5)',
    }}>
      <Story />
    </div>
  ),
],
```

```javascript
// GOOD: Simple constraints, use parameters for layout
decorators: [
  (Story) => (
    <div style={{ width: '600px', padding: '20px' }}>
      <Story />
    </div>
  ),
],
parameters: {
  layout: 'centered',
},
```

**Why**: Decorators should provide minimal structural constraints, not complex styling. If you need elaborate styling, it suggests the component might need its own wrapper.

## Anti-Pattern 2: Inconsistent Decorators Across Similar Components

```javascript
// BAD: DatePicker and TimePicker have different widths
// DatePicker.stories.js
const meta = {
  decorators: [(Story) => <div style={{ width: '300px' }}><Story /></div>],
};

// TimePicker.stories.js
const meta = {
  decorators: [(Story) => <div style={{ width: '250px' }}><Story /></div>],
};
```

```javascript
// GOOD: Consistent width for similar components
// Both DatePicker.stories.js and TimePicker.stories.js
const meta = {
  decorators: [(Story) => <div style={{ width: '300px' }}><Story /></div>],
};
```

**Why**: Similar components should have consistent presentation in Storybook. This makes comparison easier and maintains a cohesive design system view.

## Anti-Pattern 3: Using Decorators When Parameters Suffice

```javascript
// BAD: Unnecessary decorator for centering
decorators: [
  (Story) => (
    <div style={{ display: 'flex', justifyContent: 'center', alignItems: 'center', height: '100vh' }}>
      <Story />
    </div>
  ),
],
```

```javascript
// GOOD: Use layout parameter
parameters: {
  layout: 'centered',
},
```

**Why**: Storybook provides built-in parameters for common layout needs. Use those first before creating custom decorators.

## Anti-Pattern 4: Duplicating Provider Logic

```javascript
// BAD: Same provider in every story file
// ComponentA.stories.js
decorators: [
  (Story) => <QueryClientProvider client={query_client}><Story /></QueryClientProvider>,
],

// ComponentB.stories.js
decorators: [
  (Story) => <QueryClientProvider client={query_client}><Story /></QueryClientProvider>,
],

// ComponentC.stories.js
decorators: [
  (Story) => <QueryClientProvider client={query_client}><Story /></QueryClientProvider>,
],
```

```javascript
// GOOD: Global decorator in .storybook/preview.js
export const decorators = [
  (Story) => <QueryClientProvider client={query_client}><Story /></QueryClientProvider>,
];
```

**Why**: If multiple components need the same provider, make it global. Don't repeat yourself across story files.

## Anti-Pattern 5: Story-Level Decorators for Common Needs

```javascript
// BAD: Every story has the same decorator
export const Default = {
  decorators: [(Story) => <div style={{ padding: '20px' }}><Story /></div>],
  args: { ... },
};

export const WithError = {
  decorators: [(Story) => <div style={{ padding: '20px' }}><Story /></div>],
  args: { ... },
};

export const Loading = {
  decorators: [(Story) => <div style={{ padding: '20px' }}><Story /></div>],
  args: { ... },
};
```

```javascript
// GOOD: Meta-level decorator applies to all
const meta = {
  decorators: [(Story) => <div style={{ padding: '20px' }}><Story /></div>],
};

export const Default = {
  args: { ... },
};

export const WithError = {
  args: { ... },
};

export const Loading = {
  args: { ... },
};
```

**Why**: Meta-level decorators reduce duplication and ensure consistency.

## Anti-Pattern 6: Mixing Concerns in One Decorator

```javascript
// BAD: Decorator does layout AND data AND auth
decorators: [
  (Story) => {
    localStorage.setItem('user-type', 'admin');
    const query_client = new QueryClient();
    return (
      <QueryClientProvider client={query_client}>
        <div style={{ width: '600px', padding: '20px' }}>
          <Story />
        </div>
      </QueryClientProvider>
    );
  },
],
```

```javascript
// GOOD: Separate decorators for separate concerns
decorators: [
  (Story) => <QueryClientProvider client={query_client}><Story /></QueryClientProvider>,
  (Story) => <div style={{ width: '600px', padding: '20px' }}><Story /></div>,
  (Story) => {
    localStorage.setItem('user-type', 'admin');
    return <Story />;
  },
],
```

**Why**: Separation of concerns makes decorators reusable and easier to understand. Order becomes explicit.

## Anti-Pattern 7: Side Effects Without Cleanup

```javascript
// BAD: No cleanup of side effects
decorators: [
  (Story) => {
    localStorage.setItem('feature-flag', 'true');
    return <Story />;
  },
],
```

```javascript
// GOOD: Clean up after story (if needed across stories)
decorators: [
  (Story) => {
    const original_value = localStorage.getItem('feature-flag');
    localStorage.setItem('feature-flag', 'true');

    useEffect(() => {
      return () => {
        if (original_value !== null) {
          localStorage.setItem('feature-flag', original_value);
        } else {
          localStorage.removeItem('feature-flag');
        }
      };
    }, []);

    return <Story />;
  },
],
```

**Why**: Side effects can leak between stories. However, for user type mocking, we typically accept this because Storybook is refreshed between user type switches.

## When Decorators Are Wrong Choice

Don't use decorators when:

1. **Component is full-width** - No layout constraints needed
2. **Simple visual element** - AlertBox, Pagination don't need decoration
3. **Parameters exist** - Use `parameters.layout` instead
4. **Data should be in args** - Pass data as props, not via decorator
5. **Logic belongs in component** - Decorators are for story environment, not component logic

## Related Notes
- [Decorator Patterns](/storybook/decorators/patterns.md) - Correct decorator patterns
- [Decorator Placement](/storybook/decorators/placement.md) - Where to put decorators
- [Parameters Structure](/storybook/organization/parameters.md) - Alternative to decorators
