# Command Grammar

Every `fob-<tool>` CLI follows one shape:

```
fob-<tool> <resource> <action> [target] [options]
```

No exceptions. A user who learns one wrapper can drive them all. We follow the
`<resource> <action>` order like `gh`, `docker`, and `gcloud`.

The worked reference is `finopsbricks/cli/cli-fob/docs/cli-design-style.md` — this note is the
repo-neutral distillation.

## Resources

Top-level plural nouns for the thing you act on (`invoices`, `accounts`, `customers`). Typing
`fob-<tool> <resource>` with no action shows the resource's available actions — it never
defaults to one.

## Actions

Verbs that operate on a resource. Standard vocabulary — reuse these names, don't invent synonyms:

| Action | Purpose |
|---|---|
| `list` | List instances |
| `show` | Show one instance in detail (definitional — "what is this configured to be?") |
| `status` | Snapshot of live operational state ("what is it doing right now?") |
| `watch` | Live tail; streamed `status` |
| `create` | Create an instance |
| `edit` | Modify an existing instance |
| `delete` | Remove an instance |
| `pull` / `push` | Download to local / upload from local |
| `run` | Execute |

Not every resource needs every action — add only what's useful.

**Definitional vs operational.** When a resource has both a configured shape and a runtime
state, use separate verbs, not a flag on `show`: `show` reads config, `status` reads live
state, `watch` streams it. Precedent: `git status`, `kubectl get --watch`, `systemctl status`.

## Explicit actions — no inference

Always require the action. Never infer it from an argument.

```bash
fob-qbo invoices list            # correct
fob-qbo invoices                 # shows help; does NOT default to list
fob-qbo invoices 123             # error; does NOT infer "show"
```

This removes ambiguity and makes scripts self-documenting.

## Relationships: flags on `edit`, not new actions

Manage relationships (tags, assignments) as repeatable flags on `edit`, keeping the shape flat:

```bash
fob-qbo invoices edit 123 --add-tag urgent --remove-tag stale   # correct
fob-qbo invoices tag 123 urgent                                 # wrong — invents an action
```

Precedent: `gh issue edit 23 --add-label bug --remove-label stale`. CRUD on the relationship's
*own* resource stays standalone (`fob-qbo tags create urgent`); linking is a flag on the entity.

## The `config` namespace and profiles

`config` is a **namespace, not a resource** — it groups meta/setup commands, kept apart from data
resources. Credential profiles are managed under it:

```
fob-<tool> config profiles <list|add|use|remove|refresh>
```

- The object noun is the blanket **`profiles`**, family-wide — the stored thing is a named
  credential set, not inherently a domain entity. Precedent: gcloud `config configurations list`.
  Avoid the ambiguous flat form (`config list` reads as "list settings" but lists accounts).
- Each CLI **may** add a domain alias where it reads better: `orgs` (fob-stm), `mailboxes`
  (fob-email). One yargs line: `.command(['profiles', 'orgs'], …)`.
- `profiles list` prints a table with a current-`*` marker and a `config: <path>` footer.

Storage, precedence, and identity-caching details: [Config & Secrets](./config-and-secrets.md).

## `--json` everywhere

Every read command supports `--json` to emit the raw response instead of formatted text. This
is the escape hatch for scripting and for feeding an LLM. See [Output & Formatting](./output-formatting.md).

## Help at every level

```bash
fob-qbo                    # lists resources
fob-qbo invoices           # lists actions for invoices
fob-qbo invoices edit      # usage and flags for edit
```

`yargs` gives this for free with `.demandCommand()` + `.help()` at each subtree.

### Group a command's own options above the global ones

A command's help must separate **its own options** from the **global options**
every command inherits (`--profile`, `--help`, `--version`). Command options
come first under `Options:`; globals sit below under `Global Options:`. Order:
`Positionals:` → `Options:` → `Global Options:`.

```
fob-qbo config profiles add <name>

Positionals:
  name  Profile name                                         [string] [required]

Options:                          ← the command's own flags, first
      --api-key     API key                                  [string] [required]
      --api-secret  API secret                               [string] [required]

Global Options:                   ← inherited by every command, last
      --profile  Use credentials from a specific profile (overrides current)
  -h, --help     Show help
  -v, --version  Show version number
```

Without this, yargs interleaves the two — globals defined at the root sandwich
the command's flags — and there's no ordering logic to fall back on.

**yargs mechanics.** yargs merges a command's instance groups *before* the
inherited (preserved) global groups, and otherwise materialises the default
`Options:` group last. So:

1. At the root, name the global group once:
   `.group(['profile', 'help', 'version'], 'Global Options:')`.
2. In each command's builder, pre-register the command's own `Options:` group so
   it renders ahead of the globals — a one-line `localOptions()` helper:
   ```js
   export function localOptions(yargs) {
     return yargs.group([], 'Options:');   // instance group ⇒ sorts before globals
   }
   ```
   Ungrouped options then fall into that already-first group. Call it **after**
   `.positional()` (positionals register the `Positionals:` group when declared,
   and must stay first): `localOptions(y.positional('name', …)).option(…)`.

Empty groups aren't preserved into subcommands, so the `Options:` group must be
registered per-command, not once at the root. A command with no options of its
own simply shows no `Options:` heading. Guard the ordering with a help-output
test (see [Testing](./testing.md)).

### Hang-indent wrapped descriptions

A description too long for one line must continue at the **description column**,
never at column 0:

```
Commands:
  status [id]        Snapshot of live bin state for one station
                     (reads temp/stations/)          ← aligned: one entry
  empty-bins [id]    Wipe selected bin directories under
temp/stations/<STATION>/ (destructive)               ← wrong: reads as a new command
```

The name column is the visual gutter. Text starting left of it parses as a new
row; a continuation at column 0 makes the list unscannable.

**yargs mechanics.** yargs hang-indents correctly *by default* — so the rule in
practice is **don't disable wrapping**:

```js
.wrap(null)                            // ✗ no wrapping; the terminal wraps instead, unindented
.wrap(process.stdout.columns || 100)   // ✓ wraps to terminal width, hang-indented
```

`.wrap(null)` is the trap: it reads like "let the terminal handle it," but the
terminal has no notion of the description column and breaks at column 0.

One caveat yargs does *not* solve: when the name column is wide, the description
column gets narrow and yargs falls back to breaking **mid-word**
(`temp/station|s/`). Yargs exposes no help-formatter API, and it deliberately
prefixes nested rows with the full parent command path
([yargs#990](https://github.com/yargs/yargs/commit/cd1ca15)), which is what
consumes the width — unlike Cobra (`kubectl`, `gh`, `docker`), which renders bare
names. Shortening descriptions is the cheap fix; a hand-rolled renderer is the
expensive one. Prefer the former.

## Naming conventions

- **Resources**: plural, kebab-case (`work-records`, not `workRecords`).
- **Actions**: singular verbs (`list`, `create`).
- **Targets**: the id or slug being acted on.
- **Options**: kebab-case with `--` (`--add-tag`, not `--addTag`). Short aliases where useful
  (`-h`). Boolean flags take no value (`--all`); value flags require one (`--limit 10`);
  repeatable flags may appear many times (`--add-tag a --add-tag b`).

## Related Notes

- [Output & Formatting](./output-formatting.md)
- [Project Structure](./project-structure.md)
- `finopsbricks/cli/cli-fob/docs/cli-design-style.md` — the full worked example
