# Config & Secrets

Where a `fob-<tool>` keeps its config, how it stores credentials, and how it models multiple
accounts. Storage is a **layered** concern: ship a simple plaintext baseline, add encryption later
without reworking callers. Reference implementation: `finopsbricks/cli/fob-stm/src/config.js`.

## Config location

One file per tool, under a shared family root:

```
~/.fob/<tool>/config.yml        # e.g. ~/.fob/fob-stm/config.yml, mode 0600
```

- **Family root `~/.fob/`.** Every wrapper nests here, so all config/creds share one backup,
  `chmod`, or delete surface. Subdir = the **binary name** (`fob-stm`, `fob-email`).
- **Multi-OS, not XDG.** Resolve via `os.homedir()` — one deterministic path on macOS, Linux, and
  Windows. XDG is Linux-centric and forces per-OS branches (`~/Library/Application Support`,
  `%APPDATA%`); AWS/npm/fly stay off it for the same reason. OS-native dirs are a possible later layer.
- **Override** the whole dir with `FOB_<TOOL>_CONFIG_DIR` (tests, containers, CI).
- **CLI-only.** Workers import the client and read `FOB_<TOOL>_API_*` env vars — they never touch
  this file, so multi-OS here only matters for interactive CLI use.

## One file, not two

Keep a single `config.yml` — do **not** split into aws-style `config` + `credentials`. Our
non-secret data is thin, and two files add sync edge-cases. Instead separate **secret** fields from
**metadata** in the *data model* (below). That seam — not a second file — is what lets a keychain
layer relocate only the secret later. (An aws-style `credentials` file, if ever needed, is just the
no-keychain fallback of that layer.)

## Secret storage — layered

Secrets (api-key/secret, tokens, passwords) live behind one storage module (`src/config.js`), so the
representation can change without touching command handlers.

| Layer | Storage | When |
|---|---|---|
| **1 — baseline (now)** | plaintext `config.yml`, mode `0600` | ship first; works on laptops and headless servers alike |
| **2 — opt-in** | secret → OS keychain on a laptop; plaintext `0600` fallback where no keychain exists; env var for automation | when encryption-at-rest is wanted |
| **3 — later** | keychain by default + explicit insecure escape hatch | once Layer 2 is proven across the family |

Plaintext `0600` is a deliberate baseline, not negligence: it's the only thing that works
identically on a laptop and a headless worker/CI box (which have no keychain), and the env-var path
is the real production story. Reference hybrid: **Claude Code** (Keychain on macOS, `0600` file
fallback, `ANTHROPIC_API_KEY` for automation). Never log secrets; `--print-url`-style helpers build
URLs without embedding creds.

## Profiles (multiple accounts)

A CLI acts as one of several named identities. The stored object is a **credential profile** — a
named credential set (`{api_url,api_key,api_secret}`; or `{host,port,user,pass}`). Model:

- a `profiles` map + a `current` pointer;
- a `use` command to switch, and a per-command flag to override for one call;
- **precedence: flag > env (`FOB_<TOOL>_API_*`) > current profile** — universal across CLIs;
- print a one-time stderr hint naming the active identity's source (env vs config).

The command surface (`config profiles <verb>`, the `orgs`/`mailboxes` alias) lives in
[Command Grammar](./command-grammar.md).

## Profiles cache their server-resolved identity

A profile name is a **local alias** (`alex`); the credential authenticates as a specific server-side
identity the CLI can't derive from the key alone (org id + slug, mailbox address, realm). Resolve it
once and cache it as **non-secret metadata**, so the config is self-describing:

```yaml
alex:
  org_id: P8nxj0gb9hlF     # server truth, non-secret
  org_slug: alex
  api_url: https://…/
  api_key: …
  api_secret: …
```

- Resolve on `profiles add`; re-sync with `profiles refresh <name>` / `--all`.
- Server is source of truth; the cache is for display; `refresh` fixes drift.
- **Never block adding creds on the identity fetch** — save the profile, warn on stderr, fill on the
  next successful call. Adding creds must not require a network round-trip.
- Needs a server identity endpoint (e.g. `GET /api/v1/whoami` → `{ id, slug }`); expose it on the
  client as `getIdentity(credentials)` so workers can use it too.

## Related Notes

- [Auth Patterns](./auth-patterns.md) — the credentials-override seam + api-key/OAuth/protocol patterns
- [Command Grammar](./command-grammar.md) — the `config profiles <verb>` surface
- [Project Structure](./project-structure.md) — `config.js` as the credential-source layer
