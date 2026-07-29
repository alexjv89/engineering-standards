# Project Structure — the 2-in-1 lib+CLI

A wrapper is **one package that is both an importable client and a CLI over the same
functions**. This is what makes the prototype→production path a copy-paste: an FDE explores
`fob-qbo invoices list` in the terminal, then a worker does `import { getInvoices } from
'@fob/qbo'` — the *same function*.

## Three layers, kept separate

| Layer | Responsibility | Where |
|---|---|---|
| **Client core** | data in → data out. No formatting, no `console`. Takes an optional per-call credentials override. | `src/index.js` + `src/<domain>.js` / `src/utils/http.js` |
| **CLI presentation** | grammar, arg parsing, formatting, help | `bin/`, `src/cli/` |
| **Credential source** | where keys come from (env for workers, config file for the CLI) | `src/utils/config.js` |

Only the client core is imported by workers. The CLI is a thin shell that parses args, calls
the client, and formats. Keep `console.*` out of the client entirely — see
[Separate Presentation from Logic](/principles/scripting/separate-presentation-from-logic.md).

## Directory layout

```
bin/
  cli.js                     # #!/usr/bin/env node — imports run() from src/cli/index.js
src/
  index.js                   # CLIENT exports — what workers import
  <domain>.js                # client functions (getInvoices, createInvoice, …)
  cli/
    index.js                 # yargs root: builds the command tree, calls .parse()
    _helpers.js              # safe(), shared option builders
    <resource>/
      list.js                # one file per action; exports <verb><Resource>Handler(argv)
      show.js
      create.js
  utils/
    http.js                  # HTTP/client transport (or protocol engine for non-API tools)
    format.js                # output helpers (copied from a sibling wrapper)
    config.js                # credential resolution
test/ | tests/
```

File-per-action keeps handlers small and greppable. Handlers are named
`<verb><Resource>Handler` (`listInvoicesHandler`, `showAccountHandler`).

## package.json

Expose both faces:

```json
{
  "name": "@fob/qbo",
  "type": "module",
  "main": "src/index.js",
  "exports": { ".": "./src/index.js" },
  "bin": { "fob-qbo": "./bin/cli.js" }
}
```

`main`/`exports` serve the importable client; `bin` registers the CLI so the
[dispatcher](./subcommand-dispatch.md) finds `fob-qbo` on `PATH`. For the publish-time fields
(`files` allow-list, `publishConfig`, repo metadata), see [Publishing & Packaging](./publishing.md).

## Wiring: bin → yargs root → resource subtrees

`bin/cli.js` is trivial:

```js
#!/usr/bin/env node
import { run } from '../src/cli/index.js';
run(process.argv.slice(2));
```

`src/cli/index.js` builds the tree; each resource is a subtree builder that registers its
actions with `safe()`-wrapped handlers:

```js
function buildInvoicesSubcommands(yargs) {
  return yargs
    .usage('$0 invoices <action> [options]')
    .command('list', 'List invoices',
      (y) => y.option('json', { describe: 'Output raw JSON', type: 'boolean' }),
      safe(listInvoicesHandler))
    .command('show <id>', 'Show one invoice',
      (y) => y.positional('id', { type: 'string' }),
      safe(showInvoiceHandler))
    .demandCommand(1, 'Specify an action: list, show, …');
}
```

The root ends with `.demandCommand(1).strict().help().alias('h','help').version()`.

## The client core

Pure functions that return parsed data and accept a per-call credentials override — the seam
that lets one worker process reach multiple tenants, and lets the CLI inject creds from its
config file. Reference: `finopsbricks/lib/lib-worker-statements/src/statements.js`.

```js
export async function getInvoices(params = {}, credentials) {
  return apiGet('/invoices', params, credentials); // creds ?? process.env
}
```

## Exception: worker-context CLIs

Some CLIs are bound to a **single tenant and the local filesystem** — they load code and config
from the current working directory and use one API key per repo. `finopsbricks/cli/cli-fob` is
the reference: it drives a worker repo's `./src/steps`, `./.orchestrator/`, `./temp/`, and reads
one `ORCHESTRATOR_*` key from that repo's `./.env`.

For these, two rules relax deliberately — this is a recognized variant, not non-compliance:

- **CLI-only, not a 2-in-1.** There's no external worker importing a client, because the CLI
  *is* the worker-repo tool. It may still expose internal client functions
  (`src/utils/orchestrator.js`) kept free of `console.*`, but it doesn't ship an `@fob/<tool>`
  client for others to import.
- **Convention-based `.env`, not a config file.** Credentials come from the worker repo's
  `./.env` by convention, so there's no `~/.fob-<tool>/config.yml` and no per-call credentials
  override (single tenant per process — nothing to override).

Everything else — grammar, output, `safe()`, testing — applies unchanged. Reach for this
variant only when the CLI genuinely operates on the cwd; a tool that talks to a remote API for
arbitrary tenants is a 2-in-1.

## Related Notes

- [Command Grammar](./command-grammar.md)
- [Auth Patterns](./auth-patterns.md) — the credentials override in detail
- [Output & Formatting](./output-formatting.md)
