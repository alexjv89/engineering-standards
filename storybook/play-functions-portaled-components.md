# Play Functions: Portaled Components

Handle dropdowns and modals that portal outside the Storybook canvas.

## The Problem

Many dropdowns and modals use portals to render outside the component tree, which means they're not inside `canvasElement`.

## Solution: Use document.querySelector

For portaled elements, query the entire document instead of canvas:

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

## Pattern

1. **Trigger inside canvas** - Use `canvas.getByRole()` for elements inside canvasElement
2. **Wait for portal** - Use `waitFor()` with `document.querySelector()` for portaled elements
3. **Interact with portal** - Use `document.querySelector()` to find and interact

## Common Portaled Components

- **Radix UI** dropdowns, dialogs, tooltips
- **Headless UI** modals, menus
- **Custom modals** with React portals
- **Date pickers** with calendar overlays

## data-testid Pattern

Use `data-testid` for reliable queries on portaled elements:

```javascript
// Component
<DropdownMenu.Content data-testid="dropdown-menu">
  <DropdownMenu.Item data-testid="menu-item-1">Option 1</DropdownMenu.Item>
</DropdownMenu.Content>

// Story
await waitFor(() => {
  const menu = document.querySelector('[data-testid="dropdown-menu"]');
  expect(menu).toBeInTheDocument();
});
```

## Related Notes
- [Play Functions Basics](/storybook/play-functions-basics.md)
- [Play Functions Auto-Open Pattern](/storybook/play-functions-auto-open.md)
- [Play Functions Waiting](/storybook/play-functions-waiting.md)
- [Play Functions Assertions](/storybook/play-functions-assertions.md)
