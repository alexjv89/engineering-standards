# Play Functions: Waiting Strategies

Async waiting patterns for play functions.

## waitFor with Assertions

Wait for element to appear with assertion:

```javascript
import { waitFor } from 'storybook/test';

await waitFor(() => {
  expect(canvas.getByRole('menu')).toBeVisible();
});
```

## waitFor with Timeout

Specify custom timeout for slow operations:

```javascript
await waitFor(
  async () => {
    const element = document.querySelector('[data-testid="item"]');
    await expect(element).toBeInTheDocument();
  },
  { timeout: 3000 }
);
```

## Simple Delay

For animations or transitions with known duration:

```javascript
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
```

## When to Use Each

**waitFor with assertion** - When waiting for specific condition:
```javascript
await waitFor(() => {
  expect(canvas.getByText('Loaded')).toBeInTheDocument();
});
```

**waitFor with timeout** - For slower operations:
```javascript
await waitFor(
  () => expect(element).toBeVisible(),
  { timeout: 5000 }
);
```

**Simple delay** - For known animation durations:
```javascript
// Wait for 300ms CSS transition
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
```

## Avoid Long Delays

Don't use arbitrary long delays:

```javascript
// BAD - wastes time
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 2000)));

// GOOD - wait for specific condition
await waitFor(() => expect(element).toBeVisible());
```

## Related Notes
- [Play Functions Basics](/storybook/play-functions-basics.md)
- [Play Functions Auto-Open Pattern](/storybook/play-functions-auto-open.md)
- [Play Functions Portaled Components](/storybook/play-functions-portaled-components.md)
- [Play Functions Assertions](/storybook/play-functions-assertions.md)
