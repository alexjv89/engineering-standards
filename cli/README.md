# CLI Standard

How we build command-line tools at FinOpsBricks. This standard covers `fob-<tool>`
wrappers over both our own apps and external SaaS tools (QuickBooks Online, Zoho Books, …),
plus the two reference CLIs that predate it (`fob`, `fobs`).

## Philosophy

- **A documented pattern, not a framework.** There is no `cli-kit` package and no generator.
  Consistency comes from this standard + **copying proven files** (`format.js`, `safe()`, the
  yargs skeleton, an auth pattern) into each new CLI + review against these notes. Copying is
  the deliberate trade: a fix to `format.js` does not propagate, but we avoid a shared-package
  boundary and version coupling. Revisit extraction only past ~15 wrappers.
- **Every CLI is a 2-in-1.** A wrapper is an importable client library *and* a CLI over the
  same functions. Workers `import { getInvoices } from '@fob/<tool>'`; the CLI calls the same
  `getInvoices`. Prototyping in the terminal and shipping into a worker is copy-paste, not a
  rewrite. See [Project Structure](./project-structure.md).
- **Familiar grammar.** `resource action target options`, like `gh`/`docker`/`gcloud`. A user
  who knows one `fob-<tool>` knows them all. See [Command Grammar](./command-grammar.md).

## The notes

| Note | Covers |
|---|---|
| [Command Grammar](./command-grammar.md) | `resource action target options`, verbs, help, naming |
| [Project Structure](./project-structure.md) | The 2-in-1 lib+CLI layout, layers, package.json |
| [Output & Formatting](./output-formatting.md) | `--json`, table/field helpers, column selector, stdout vs stderr |
| [Error Handling](./error-handling.md) | `safe()` wrapper, exit codes, validation messages |
| [Auth Patterns](./auth-patterns.md) | api-key and OAuth2-refresh; creds file; per-call override |
| [Config & Secrets](./config-and-secrets.md) | `~/.fob/<tool>/` location, plaintext-`0600` baseline + keychain layering, profiles, identity caching |
| [Subcommand Dispatch](./subcommand-dispatch.md) | git-style `fob <tool>` launcher |
| [Testing](./testing.md) | Jest ESM, `captureOutput()`, mock the client |

## Building a new `fob-<tool>` wrapper — checklist

1. Scaffold the [2-in-1 structure](./project-structure.md): `bin/`, `src/index.js` (client
   exports), `src/cli/` (yargs shell), `src/utils/`.
2. Write the **client** first (pure data in/out, per-call credentials override). This is what
   workers import and what the CLI calls.
3. Copy `src/utils/format.js`, `src/cli/_helpers.js` (`safe()`), and the test
   `captureOutput()` helper from the nearest existing wrapper.
4. Pick the [auth pattern](./auth-patterns.md) that matches the tool (api-key vs OAuth2-refresh),
   and set up [config & secrets](./config-and-secrets.md): `~/.fob/<tool>/config.yml` (`0600`), the
   `config profiles` surface, plaintext baseline behind the `config.js` seam.
5. Map the tool's resources/actions onto the [grammar](./command-grammar.md). Not every
   resource needs every verb.
6. Add `--json` to every read command; keep data on stdout, diagnostics on stderr.
7. Register the binary as `fob-<tool>` so the [dispatcher](./subcommand-dispatch.md) finds it.

## Reference implementations

- `finopsbricks/cli/cli-fob` — worker-context CLI; source of `docs/cli-design-style.md`.
- `finopsbricks/cli/cli-fobs` — API-client CLI; source of `safe()`, `format.js`, column selector.
- `finopsbricks/lib/lib-email` — the original 2-in-1 lib+CLI shape.

## Related Notes

- [Separate Presentation from Logic](/principles/scripting/separate-presentation-from-logic.md)
- WIP: `docs/wip/cli-standards-and-wrappers.md`
