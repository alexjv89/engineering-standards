# Auth Patterns

Auth is the biggest per-tool variable and the reason third-party CLIs exist at all. The
standard does **not** ship a pluggable auth framework — each wrapper implements the pattern its
tool needs. Two patterns cover almost everything.

## The credentials override seam (both patterns)

Client functions read credentials from the environment by default, but accept an explicit
per-call override. This one seam serves two callers: a worker reaching multiple tenants from
one process, and the CLI injecting creds resolved from its config file
(`finopsbricks/lib/lib-worker-statements/src/statements.js`):

```js
const getHeaders = (credentials) => ({
  'api-key':    credentials?.api_key    ?? process.env.FOB_QBO_API_KEY,
  'api-secret': credentials?.api_secret ?? process.env.FOB_QBO_API_SECRET,
  'Content-Type': 'application/json',
});
```

**Env var convention:** `FOB_<TOOL>_API_URL`, `FOB_<TOOL>_API_KEY`, `FOB_<TOOL>_API_SECRET`.

## Pattern A — api-key / secret (FinOpsBricks apps)

Static key + secret sent as headers. The CLI stores them in a YAML config file and passes them
as the override.

- **Config file:** `~/.fob-<tool>/config.yml`, mode `0600`, created on first write. (The
  `fobs` aggregator uses one `~/.fobs/config.yml` keyed per org-per-app.)
- **Resolution precedence** (from `cli-fobs`): `--org`/flag override → local `./.env` → config
  file's current selection. Each step is explicit; no silent fallback.

```yaml
# ~/.fob-stm/config.yml
current_org: alex
orgs:
  alex:
    api_url: https://statements.finopsbricks.com/
    api_key: fob_stm_xxxxxxxx
    api_secret: yyyyyyyy
```

## Pattern B — OAuth2 with refresh token (external tools: QBO, Zoho Books)

External vendors use OAuth2. The gnarly parts are refresh-token exchange, access-token expiry,
and a tenant/realm id. The wrapper owns this so callers never see it:

1. Store the long-lived **refresh token** + **tenant id** (QBO `realmId`, Zoho `organization_id`)
   in `~/.fob-<tool>/config.yml` (0600).
2. On each call, if the cached **access token** is missing or expired, exchange the refresh
   token at the vendor's token endpoint and cache the new access token with its expiry.
3. Send `Authorization: Bearer <access_token>` + the tenant id.
4. Provide `fob-<tool> auth login` (runs the OAuth consent flow, stores the refresh token) and
   `fob-<tool> auth status` (shows tenant + token expiry).

```
~/.fob-qbo/config.yml
  realm_id: 1234567890
  refresh_token: <long-lived>
  access_token: <cached>          # refreshed automatically
  access_token_expires_at: <iso>
```

Keep the token machinery in the client core, behind the same `getHeaders(credentials)` seam, so
a worker can pass a stored refresh token as an override just like an api-key.

## Pattern C — protocol / connection credentials (IMAP, SMTP, SSH, databases)

Not every tool is an HTTP API. Protocol tools authenticate a live connection with a
host/port/user/pass (+ TLS) tuple rather than a header, and hold a stateful session instead of
making independent stateless calls. `finopsbricks/lib/lib-email` is the reference.

- **Same override seam.** Credential resolution still layers env → config file → per-call
  override, and the client still accepts an explicit credentials object. `resolveAccount(name)`
  takes either a named account (resolved from `FOB_<TOOL>_ACCOUNTS` / a YAML config) or a raw
  config object, so a worker can pass connection details directly.
- **Validate on connect**, with a schema (lib-email uses zod), so a bad host/port fails with a
  clear message before any protocol traffic.
- **Expose a `Session` for reuse** alongside one-shot helpers. Stateless HTTP handlers open no
  connection; protocol handlers should batch through a `connect() → … → close()` session and
  offer one-shot wrappers (`listEmails`) for single operations.
- Config file: `~/.fob-<tool>/config.yml` (0600), same as the other patterns.

Where the HTTP-centric machinery doesn't map: there's no `http.js`, no `{ data, page_context }`
envelope, and no `ApiError` with a status code — the transport is the protocol engine
(`src/engine/imap.js`) and it throws plain `Error`s. The [`safe()`](./error-handling.md) wrapper
still applies unchanged; typed transport errors are optional. Pagination via `page_context` is
replaced by a simple `--limit`.

## Rules

- Config files are `0600`; never log secrets. `--print-url`-style helpers must build the URL
  without embedding credentials.
- Print a one-time stderr hint when creds come from a non-obvious source (e.g. a local `./.env`
  overriding the config file), so the user knows which identity is in play.

## Related Notes

- [Project Structure](./project-structure.md) — where the client core lives
- [Error Handling](./error-handling.md) — surfacing "no credentials" clearly
