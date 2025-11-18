# Storybook Interactions: Play Functions and Testing

Use play functions to auto-interact with components and add test assertions in stories.

## Basic Play Function

```javascript
import { userEvent, within, expect, waitFor } from 'storybook/test';

export const InteractiveTest = {
  render: () => <Dropdown />,
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Find and click button
    const trigger = canvas.getByRole('button');
    await userEvent.click(trigger);

    // Wait for dropdown to appear
    await waitFor(() => {
      expect(canvas.getByRole('menu')).toBeVisible();
    });
  },
}
```

## Auto-Open Pattern

Common pattern for dropdowns and dialogs:

```javascript
const autoOpenDropdown = async ({ canvasElement }) => {
  const canvas = within(canvasElement);
  const trigger = canvas.getByRole('button', { name: /more options/i });
  await userEvent.click(trigger);
  await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
};

export const AfterSuccessfulProcessing = {
  args: { statement: mockStatement },
  play: autoOpenDropdown,
}
```

## Test Assertions in Stories

Use `expect()` to add test assertions:

```javascript
export const FormValidationTest = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Type into input
    const input = canvas.getByRole('textbox');
    await userEvent.type(input, 'test@example.com');

    // Verify value
    await expect(input).toHaveValue('test@example.com');

    // Clear input
    await userEvent.clear(input);

    // Verify cleared
    await expect(input).toHaveValue('');
  },
}
```

## Using step() for Clarity

Group actions into named steps:

```javascript
export const InteractiveTest = {
  play: async ({ canvasElement, step }) => {
    const canvas = within(canvasElement);

    await step('Open dropdown', async () => {
      const trigger = canvas.getByRole('button');
      await userEvent.click(trigger);
    });

    await step('Select menu item', async () => {
      const menuItem = document.querySelector('[data-testid="item-1"]');
      await userEvent.click(menuItem);
    });

    await step('Verify selection', async () => {
      const selected = canvas.getByText('Item 1');
      await expect(selected).toBeInTheDocument();
    });
  },
}
```

## Handling Portaled Components

For dropdowns/modals that portal outside the canvas:

```javascript
export const DropdownTest = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Click trigger (inside canvas)
    const trigger = canvas.getByRole('button');
    await userEvent.click(trigger);

    // Wait for portaled dropdown (outside canvas)
    await waitFor(async () => {
      const menuItem = document.querySelector('[data-testid="menu-item"]');
      await expect(menuItem).toBeInTheDocument();
    });

    // Interact with portaled element
    const menuItem = document.querySelector('[data-testid="menu-item"]');
    await userEvent.click(menuItem);
  },
}
```

## Common Interactions

### Click

```javascript
const button = canvas.getByRole('button');
await userEvent.click(button);
```

### Type

```javascript
const input = canvas.getByRole('textbox');
await userEvent.type(input, 'Hello world');
```

### Clear

```javascript
const input = canvas.getByRole('textbox');
await userEvent.clear(input);
```

### Keyboard

```javascript
await userEvent.keyboard(' ');      // Space
await userEvent.keyboard('{Enter}'); // Enter
await userEvent.keyboard('{Escape}'); // Escape
```

### Focus

```javascript
const element = canvas.getByRole('button');
element.focus();
await expect(element).toHaveFocus();
```

## Common Assertions

```javascript
// DOM presence
await expect(element).toBeInTheDocument();
await expect(element).toBeVisible();

// Text content
await expect(element).toHaveTextContent('text');

// Attributes
await expect(element).toHaveAttribute('placeholder', 'text');
await expect(element).toHaveValue('value');
await expect(element).toHaveClass('class-name');

// States
await expect(element).toBeEnabled();
await expect(element).toBeDisabled();
await expect(element).toBeChecked();
await expect(element).toHaveFocus();

// Styling
await expect(input.className).toContain('bg-primary/5');
```

## Waiting Patterns

### waitFor with Timeout

```javascript
await waitFor(
  async () => {
    const element = document.querySelector('[data-testid="item"]');
    await expect(element).toBeInTheDocument();
  },
  { timeout: 3000 }
);
```

### Simple Delay

```javascript
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
```

## When to Use Play Functions

Use play functions for:
- **Auto-opening** dropdowns/dialogs for visual review
- **Testing interactions** like form submission
- **Validating behavior** with assertions
- **Visual regression testing** of interactive states

Don't use for:
- Stories meant for manual interaction testing
- Static display-only components

## Related Notes
- [Storybook User Journey Pattern](./storybook-user-journey-pattern.md)
- [Storybook Mock Data](./storybook-mock-data.md)
