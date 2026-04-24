# Horizontal Scroll Container

Contain overflowing inline content (tabs, stats bars, tag lists) within its parent using horizontal scroll instead of letting it spill over the page.

## Problem

When a flex row of items (tabs, stat cards, tags) exceeds the container width, the content overflows and causes a horizontal scrollbar on the entire page. This is especially common with dynamic content where the number of items varies.

## Pattern

The scroll goes on a **wrapper div**, not on the items container itself. The items container uses `w-max` to size to its content.

```jsx
{/* Wrapper — handles scroll and hides scrollbar */}
<div className="overflow-x-auto scrollbar-hide">
  {/* Items — sized to content, never wraps */}
  <div className="flex flex-nowrap w-max">
    {items.map(item => (
      <div key={item.id} className="whitespace-nowrap shrink-0">
        {item.label}
      </div>
    ))}
  </div>
</div>
```

### Key Classes

| Class | Purpose |
|---|---|
| `overflow-x-auto` | Enables horizontal scroll on wrapper |
| `scrollbar-hide` | Hides scrollbar visually (custom utility) |
| `flex-nowrap` | Prevents items from wrapping to next line |
| `w-max` | Sizes container to content width |
| `whitespace-nowrap` | Prevents text wrapping inside items |
| `shrink-0` | Prevents flex items from shrinking |
| `min-w-0` | On parent flex items, breaks default min-width so container can shrink |

### The `scrollbar-hide` Utility

Add this to `globals.css` if not already present:

```css
@layer utilities {
  .scrollbar-hide::-webkit-scrollbar {
    display: none;
  }
  .scrollbar-hide {
    -ms-overflow-style: none;
    scrollbar-width: none;
  }
}
```

## Common Pitfall

Adding `overflow-x-auto` only to the inner items container often doesn't work — the wrapper `div` and its ancestors must also constrain width. Use `min-w-0` on any flex parent that sits between the scroll container and the page root. Without it, the flex default `min-width: auto` prevents the container from shrinking below its content size.

## Examples

### Tabs

```jsx
<div className="border-b border-border overflow-x-auto scrollbar-hide">
  <TabsList className="flex-nowrap w-max">
    {tabs.map(tab => (
      <TabsTrigger key={tab.id} className="whitespace-nowrap shrink-0">
        {tab.label}
      </TabsTrigger>
    ))}
  </TabsList>
</div>
```

### Stats Bar

```jsx
<div className="flex gap-3 overflow-x-auto scrollbar-hide">
  {stats.map(stat => (
    <div key={stat.label} className="shrink-0">
      <div className="text-sm text-muted-foreground">{stat.label}</div>
      <div className="text-lg font-semibold">{stat.value}</div>
    </div>
  ))}
</div>
```

## Related Notes

- [Component Composition](/architecture/components/component-composition.md)
