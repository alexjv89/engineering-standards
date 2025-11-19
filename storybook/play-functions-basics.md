# Play Functions: Basics

Use play functions to auto-interact with components and add test assertions in Storybook stories.

## What are Play Functions?

Play functions automatically execute interactions when a story loads, useful for testing and auto-opening UI.

## Basic Pattern

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

## Using step() for Clarity

Group actions into named steps for better readability:

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
- [Play Functions Auto-Open Pattern](/storybook/play-functions-auto-open.md)
- [Play Functions Portaled Components](/storybook/play-functions-portaled-components.md)
- [Play Functions Assertions](/storybook/play-functions-assertions.md)
- [Play Functions Waiting](/storybook/play-functions-waiting.md)
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md)
