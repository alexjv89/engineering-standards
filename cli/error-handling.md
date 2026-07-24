# Error Handling

Handlers throw; a single wrapper turns exceptions into a clean message and exit code. No
handler prints its own stack trace or manages exit codes for unexpected failures.

## The `safe()` wrapper

Every handler is registered through `safe()` (`finopsbricks/cli/cli-fobs/src/cli/_helpers.js`).
It catches, prints `Error: <message>` to stderr, and exits `1`. The full stack is available
behind a debug env var:

```js
export function safe(handler) {
  return async (argv) => {
    try {
      await handler(argv);
    } catch (err) {
      console.error(`Error: ${err.message}`);
      if (process.env.FOB_DEBUG) console.error(err.stack);
      process.exit(1);
    }
  };
}
```

Register with it in the subtree builder: `.command('list', '…', builder, safe(listHandler))`.

## Exit codes

| Code | Meaning |
|---|---|
| `0` | Success |
| `1` | Runtime error (thrown by a handler, caught by `safe()`) |
| `2` | Usage error (bad/missing args) — yargs emits this via `.strict()` + `.demandCommand()` |

Handlers throw for runtime failures; they do not hand-roll usage validation that yargs already
enforces.

## Validation messages use the CLI's own vocabulary

When a handler validates input, phrase the error in the flags the user typed — not the API's
field names. The user typed `--from`, so the error says `--from`, even though the backend calls
it `date_from`:

```js
export function validateDateRange({ from, to }) {
  const DATE_RE = /^\d{4}-\d{2}-\d{2}$/;
  if (from && !DATE_RE.test(from)) throw new Error('--from must be in YYYY-MM-DD format');
  if (to && !DATE_RE.test(to))     throw new Error('--to must be in YYYY-MM-DD format');
  if (from && to && from > to)     throw new Error('--from must be before or equal to --to');
}
```

## Typed transport errors

The client/transport layer throws a typed error carrying the machine-readable code and HTTP
status, so handlers (or `--json` callers) can distinguish failures
(`finopsbricks/cli/cli-fobs/src/utils/http.js`):

```js
export class ApiError extends Error {
  constructor(message, { code, status }) {
    super(message);
    this.name = 'ApiError';
    this.code = code;
    this.status = status;
  }
}
```

Prefer the server's `error.message` over a bare HTTP status when the response carries one.

## Related Notes

- [Testing](./testing.md) — asserting on exit code + stderr via `captureOutput()`
- [Output & Formatting](./output-formatting.md) — stdout vs stderr
