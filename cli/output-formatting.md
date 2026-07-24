# Output & Formatting

Output must be readable by a human at a terminal, pipeable to another command, and consumable
by an LLM. Three rules deliver all three.

## 1. Data on stdout, everything else on stderr

`console.log` is for the payload; `console.error` is for hints, warnings, and "wrote file"
notices. This keeps `fob-qbo invoices list --json | jq …` clean — diagnostics never pollute
the pipe. The list handler's file-output path prints its confirmation to stderr for exactly
this reason:

```js
function emit(text, outputPath) {
  if (outputPath) {
    writeFileSync(outputPath, text);
    console.error(`Wrote ${outputPath}`);   // stderr — not part of the data
  } else {
    console.log(text);
  }
}
```

## 2. `--json` on every read command

Format for humans by default; emit the raw response under `--json`. It's the first branch in a
read handler:

```js
export async function showAccountHandler(argv) {
  const result = await apiGet(`/api/v1/accounts/${encodeURIComponent(argv.id)}`);
  if (argv.json) {
    console.log(JSON.stringify(result, null, 2));
    return;
  }
  // …formatted output
}
```

## 3. Use the shared `format.js` helpers

Copy `format.js` from a sibling wrapper — don't hand-roll table math per command. The
canonical set (from `finopsbricks/cli/cli-fobs/src/utils/format.js`):

| Helper | Output |
|---|---|
| `formatField(label, value, width)` | `Name:      Acme Corp` (aligned label: value) |
| `formatTable(headers, rows, {align})` | dynamic-width table, `-` for empty cells |
| `formatCsv(headers, rows)` | RFC-4180 CSV |
| `formatCurrency(v)` / `formatAmount(milli)` | thousands + 2dp |
| `formatDate(iso)` | `2026-03-15 10:23:01` |
| `formatDuration(start, end)` | `2h 15m`, `3d 4h` |
| `formatHeader(label, id, status)` | header with right-aligned status |
| `formatSection(title)` | `--- Title ---` divider |
| `formatPaginationHint(pageContext)` | `(showing 100 of 5000 — page 2; use --page 3 for more)` |

## List commands: the column selector

The column selector is **recommended for API CLIs with many wide list endpoints**, and
**optional for small domain CLIs** where fixed columns are clearer than a `--fields` vocabulary
(`cli-fob` omits it deliberately). Use it when lists have enough columns that users need to
pick, or when CSV/JSON export matters.

`list` handlers are the one place worth a shared abstraction. Define a `COLUMNS` map of
`{ header, align, render, raw }` per field, plus default and public field lists, and build a
selector (`finopsbricks/cli/cli-fobs/src/utils/list.js`):

```js
const selector = buildColumnSelector({
  columns: COLUMNS,
  defaultFields: DEFAULT_FIELDS,
  publicFields: PUBLIC_FIELDS,
});
const fields = selector.parseFields(argv.fields);   // validates --fields
console.log(formatTable(
  selector.headersFor(fields),
  selector.renderRows(items, fields),           // render = formatted/truncated
  { align: selector.alignFor(fields) },
));
```

This gives every `list` the same `--fields`, `--format` (table/csv/json), and `--output`
semantics and the same error message for an unknown field. `render` is for the table view;
`raw` (untruncated) is for CSV/JSON export.

## Pagination

Show a hint when more rows exist (`formatPaginationHint`). For bulk `--format csv|json`, use an
auto-paginating fetch (`apiGetAll` walks `page_context.has_more`) and print a truncation notice
to stderr if a safety cap is hit.

## Related Notes

- [Command Grammar](./command-grammar.md) — `--json` and flag conventions
- [Separate Presentation from Logic](/principles/scripting/separate-presentation-from-logic.md)
