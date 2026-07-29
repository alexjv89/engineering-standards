# Publishing & Packaging

How a `fob-<tool>` wrapper ships to npm. Covers what goes in the tarball, what must never,
and the publish flow. The reference published wrapper is `@highlyreco/cli`.

## The tarball: a `files` allow-list, never an `.npmignore` deny-list

Declare exactly what ships with a `files` array in `package.json`. **Do not** rely on
`.npmignore` or npm's default "everything not gitignored" behavior.

```json
"files": ["bin", "src", "README.md", "CHANGELOG.md", "LICENSE"]
```

Why allow-list, not deny-list — **it's the difference between fail-closed and fail-open:**

- An **allow-list excludes by default.** A new `.claude/`, a stray `.env`, scratch files, a
  future secrets fixture — none ship unless you explicitly add them. Safe by omission.
- A **deny-list includes by default.** Every new sensitive file ships until someone remembers
  to list it. The day you forget is the day you leak.

npm packs a wrapper's **entire working tree by default** (minus a short built-in ignore set) —
so with no `files` field, `npm pack` bundles `.claude/settings.local.json` (local absolute
paths, internal repo names, dev command history), `.env.example`, `docs/`, `tests/`, and tool
config. The `files` allow-list is the single line that closes all of it.

`package.json`, `README`, `LICENSE`, and the `main` file are **always included** by npm
regardless of `files`; a listed file that doesn't exist yet (e.g. `LICENSE`) is silently
skipped. So the list above ships `bin/`, `src/`, and those — nothing else.

## What must never be in the tarball

| Excluded | Why |
|---|---|
| `.claude/`, `.cursor/`, editor state | local paths, internal repo names, command history — **info disclosure** |
| `.env`, `.env.example` | credentials or credential-shaped placeholders; no value to installers |
| `docs/`, `docs/wip/` | internal planning notes |
| `tests/`, `test/` | dead weight for consumers |
| `jest.config.*`, `jsconfig.json`, `tsconfig.json`, `.eslintrc*` | dev tooling config |
| `package-lock.json` | npm excludes it from tarballs by default anyway |

The `files` allow-list excludes all of these automatically — this table is the intent, not a
second list to maintain.

## Two exposure channels — don't conflate them

Packaging closes the **npm tarball**. A **public GitHub repo** is a *separate* surface with
*different* leaks:

- `.claude/settings.local.json` is usually **untracked** → safe in a public repo, but leaks in
  the tarball (hence `files`).
- `docs/wip/*` is usually **tracked** → excluded from the tarball by `files`, but visible in a
  public repo. `git rm` it (or sanitize) before making the repo public.

Before flipping a repo public, also **scan full git history** for secrets
(`git log -p | grep -iE 'secret|api[-_]?key|password|token'`, or gitleaks/trufflehog) — making
a repo public exposes every past commit, not just the current tree.

## Scoped-package publish config

Wrappers publish under the org scope: **`@finopsbricks/fob-<tool>`** (the binary stays
`fob-<tool>`). Scoped packages default to **restricted** — a plain `npm publish` on a scoped
name publishes privately or fails. Opt into public explicitly:

```json
"publishConfig": { "access": "public" }
```

Also set the discoverability/repo metadata for a public package:

```json
"repository": { "type": "git", "url": "git+https://github.com/finopsbricks/fob-<tool>.git" },
"homepage": "https://github.com/finopsbricks/fob-<tool>#readme",
"bugs": "https://github.com/finopsbricks/fob-<tool>/issues",
"license": "MIT"
```

Add a `LICENSE` file to match the declared `license`.

## Publish flow

1. **Verify the tarball first:** `npm pack --dry-run` and read the file list. Confirm only
   `bin/`, `src/`, `README.md`, `CHANGELOG.md`, `package.json` (+ `LICENSE`) appear — no
   `.claude`, `.env*`, `docs`, `tests`, or tool config. This is a required pre-publish check.
   (npm's `notice` lines go to **stderr**; capture `2>&1` when grepping them.)
2. Bump the version + add a `CHANGELOG.md` entry (see [Keep a Changelog]). First public release
   is typically `0.x → 1.0.0` or a deliberate `0.y.0`.
3. `npm login` as a member of the `finopsbricks` org.
4. `npm publish` (add `--access public` if `publishConfig` isn't honored on the first publish
   of a new scoped name).
5. Confirm with a clean `npm install -g @finopsbricks/fob-<tool>` in a scratch dir.

[Keep a Changelog]: https://keepachangelog.com/en/1.1.0/

## Related Notes

- [Project Structure](./project-structure.md) — the `package.json` two-faces (`main`/`exports`/`bin`)
- [Config & Secrets](./config-and-secrets.md) — the credential file the CLI writes at runtime (separate from the tarball)
- [Subcommand Dispatch](./subcommand-dispatch.md) — how the `fob-<tool>` binary is discovered
