# Play Functions: Assertions and Queries

Test assertions and common queries for play functions.

## Test Assertions

Use `expect()` to add test assertions in play functions:

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
await userEvent.keyboard(' ');        // Space
await userEvent.keyboard('{Enter}');  // Enter
await userEvent.keyboard('{Escape}'); // Escape
```

### Focus
```javascript
const element = canvas.getByRole('button');
element.focus();
await expect(element).toHaveFocus();
```

## Common Assertions

### DOM Presence
```javascript
await expect(element).toBeInTheDocument();
await expect(element).toBeVisible();
```

### Text Content
```javascript
await expect(element).toHaveTextContent('text');
```

### Attributes
```javascript
await expect(element).toHaveAttribute('placeholder', 'text');
await expect(element).toHaveValue('value');
await expect(element).toHaveClass('class-name');
```

### States
```javascript
await expect(element).toBeEnabled();
await expect(element).toBeDisabled();
await expect(element).toBeChecked();
await expect(element).toHaveFocus();
```

### Styling
```javascript
await expect(input.className).toContain('bg-primary/5');
```

## Related Notes
- [Play Functions Basics](/storybook/play-functions/basics.md)
- [Play Functions Auto-Open Pattern](/storybook/play-functions/auto-open.md)
- [Play Functions Portaled Components](/storybook/play-functions/portaled-components.md)
- [Play Functions Waiting](/storybook/play-functions/waiting.md)
