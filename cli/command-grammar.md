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
