# Play Functions: Auto-Open Pattern

Common pattern for automatically opening dropdowns and dialogs when story loads.

## Auto-Open Pattern

Extract auto-open logic into reusable function:

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

## When to Use

Auto-open pattern is useful for:
- **Dropdowns** - Open to show menu options
- **Dialogs** - Open to show dialog content
- **Tooltips** - Show tooltip on hover
- **Collapsed sections** - Expand to show content

## Benefits

1. **Visual Review** - Reviewers see the complete UI immediately
2. **Reusability** - Same auto-open function across multiple stories
3. **Consistency** - Standardized opening behavior

## Example with Different States

```javascript
const autoOpenDialog = async ({ canvasElement }) => {
  const canvas = within(canvasElement);
  const button = canvas.getByRole('button', { name: /open/i });
  await userEvent.click(button);
  await waitFor(() => new Promise((resolve) => setTimeout(resolve, 200)));
};

export const SuccessState = {
  args: { status: 'success', message: 'File uploaded successfully' },
  play: autoOpenDialog,
}

export const ErrorState = {
  args: { status: 'error', message: 'Upload failed' },
  play: autoOpenDialog,
}
```

## Related Notes
- [Play Functions Basics](/storybook/play-functions-basics.md)
- [Play Functions Portaled Components](/storybook/play-functions-portaled-components.md)
- [Play Functions Waiting](/storybook/play-functions-waiting.md)
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md)
