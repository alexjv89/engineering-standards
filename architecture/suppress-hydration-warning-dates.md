# suppressHydrationWarning for Dates

Use `suppressHydrationWarning` on elements that render locale-dependent dates to prevent React hydration mismatches.

## Problem

`toLocaleString()`, `toLocaleDateString()`, and `toLocaleTimeString()` produce different output depending on the runtime locale. The server and browser typically have different locales, so the server-rendered HTML won't match the client — causing a React hydration error.

```
// Server renders: 24/01/2026, 00:12:43
// Client renders: 1/24/2026, 12:12:43 AM
```

This is expected behavior — the client's locale is the correct one.

## Pattern

Add `suppressHydrationWarning` to the **DOM element that directly contains** the date text:

```jsx
<p suppressHydrationWarning>
  {new Date(record.created_at).toLocaleString()}
</p>
```

For conditional rendering:

```jsx
<td suppressHydrationWarning>
  {record.started_at
    ? new Date(record.started_at).toLocaleString()
    : "-"}
</td>
```

## Scope

`suppressHydrationWarning` is **localized** — it only applies to the specific DOM element it's placed on. It does not affect parent or child elements.

```jsx
{/* Only the <span> suppresses the warning, nothing else in the component */}
<div>
  <p>Other content hydrates normally</p>
  <span suppressHydrationWarning>
    {new Date(item.created_at).toLocaleString()}
  </span>
</div>
```

## When to Use

- Rendering dates with `toLocaleString()` and related methods
- Any locale-dependent formatting where the client value is intentionally correct

## When NOT to Use

- As a blanket fix for hydration issues caused by bugs (e.g. conditional logic differing between server/client)
- On elements that don't contain locale-dependent content

## Alternatives Considered

| Approach | Trade-off |
|---|---|
| Fixed locale (`toLocaleString('en-US')`) | Forces all users into one format |
| Client-only rendering (`useEffect`) | Causes layout shift, flicker |
| `suppressHydrationWarning` | Cleanest — lets locale work as intended |

## Related Notes
- [Error Boundaries](./error-boundaries.md)
