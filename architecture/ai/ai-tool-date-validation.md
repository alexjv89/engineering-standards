# AI Tool Date Parameter Validation

Use Zod's `.datetime({ offset: true })` for timestamp parameters. No manual normalization needed.

## Pattern

```javascript
inputSchema: z.object({
  start: z.string()
    .datetime({ offset: true })
    .describe("Start timestamp in ISO 8601 with timezone (e.g., 2024-07-17T08:00:00+05:30)"),
  end: z.string()
    .datetime({ offset: true })
    .describe("End timestamp in ISO 8601 with timezone"),
}),

execute: async (input) => {
  const { start, end } = input;
  // start/end are validated - use directly, no normalization needed
  const data = await vimanaSVC.getMetrics({ start, end });
  return data;
}
```

## Valid Formats

```javascript
"2024-07-17T08:00:00+05:30"  // ✅ With timezone offset
"2024-07-17T08:00:00Z"       // ✅ UTC
"2024-07-17T08:00:00.123Z"   // ✅ With milliseconds
```

## Invalid Formats (Rejected by Zod)

```javascript
"2024-07-17"                 // ❌ Date only
"2024-07-17T08:00:00"        // ❌ No timezone
"07/17/2024"                 // ❌ Wrong format
```

## Anti-Pattern

```javascript
// ❌ DON'T manually normalize - Zod already validated
execute: async ({ start, end }) => {
  if (start.length == 10) {
    start = start + "T13:00:00.000Z";  // Unnecessary
  }
}
```

## Related Notes
- [AI Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md)
- [AI Tool Input Pattern](/architecture/ai/ai-tool-input-pattern.md)
- [Date Range Filtering](/architecture/api/date-range-filtering.md) - the same instant-only rule at the HTTP edge
