# Subcommand Dispatch

A single front door — `fob <tool> …` — that resolves to independently installed `fob-<tool>`
binaries, exactly how `git foo` finds and execs `git-foo` on `PATH`.

## Why this shape

Each wrapper is its own package: separately installed, versioned, released, and (for external
tools) separately authenticated. We want one entry point for discoverability **without** an
aggregator binary that imports every wrapper and version-couples them. Git's dispatch model
gives precisely that — the launcher is thin sugar that knows nothing about any tool.

## Behavior

```bash
fob qbo invoices list     # execs `fob-qbo invoices list`
fob stm accounts show 42  # execs `fob-stm accounts show 42`
fob                       # lists discovered fob-* binaries on PATH
fob --help                # same discovery + usage
```

- Resolve `fob <tool>` → the `fob-<tool>` executable on `PATH`. If absent, print the discovered
  wrappers and a hint to install the missing one.
- Pass remaining argv through **unchanged**; forward the child's exit code verbatim so
  `--help`, exit codes, and pipes behave as if the wrapper were called directly.
- Discovery = scan `PATH` for `fob-*` executables (dedupe, strip the `fob-` prefix).

## Sketch

```js
#!/usr/bin/env node
import { spawn } from 'node:child_process';
const [tool, ...rest] = process.argv.slice(2);
if (!tool || tool === '--help') { listWrappers(); process.exit(0); }
const child = spawn(`fob-${tool}`, rest, { stdio: 'inherit' });
child.on('exit', (code) => process.exit(code ?? 0));
child.on('error', () => { console.error(`Unknown tool '${tool}'.`); listWrappers(); process.exit(2); });
```

## ⚠️ Naming collision to resolve first

`finopsbricks/cli/cli-fob` already ships a `fob` binary for **worker-context** commands (steps,
lines, workpieces — see its `sor-cli-convergence.md`). The dispatcher wants the same name. Two
options, to be decided before building this (tracked in the WIP):

1. The dispatcher **becomes** `fob`, and today's worker-context commands move under a dispatched
   `fob-worker`.
2. The dispatcher takes a different name and `fob` stays the worker CLI.

Do not build the launcher until this is settled.

## Related Notes

- [Project Structure](./project-structure.md) — `bin` naming that makes a wrapper discoverable
- WIP: `docs/wip/cli-standards-and-wrappers.md`
