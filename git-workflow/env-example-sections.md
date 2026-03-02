# .env.example: Section Structure

Organize `.env.example` into sections by when the variable is needed, not by topic.

## Sections

```bash
# ─── Runtime ─────────────────────────────────────────────────────────────────
# Required to run the app server

DATABASE_URL=postgresql://localhost:5432/myapp_dev
AUTH_SERVICE_URL=http://auth.local.example.com:5001
INTERNAL_API_SECRET=change-me

# ─── Scripts ─────────────────────────────────────────────────────────────────
# Used by scripts/ only — not needed to run the server

DB_AUTH=postgresql://localhost:5432/auth_dev

# ─── Build-time ──────────────────────────────────────────────────────────────
# Not needed to run the dev server — only required during CI/CD builds

SENTRY_AUTH_TOKEN=
```

## Why Sections Matter

A flat `.env.example` forces developers to guess which vars are needed just to
start the dev server. Sections make this explicit — the `Runtime` section is the
minimum required, everything else is optional for local development.

## Startup Validation Scope

Only `Runtime` vars belong in the startup validation check. `Scripts` and
`Build-time` vars will not be present in all environments and should not be
required at server start.

## Related Notes

- [Environment File Structure](/git-workflow/environment-file-structure.md)
- [Environment Variable Loading](/git-workflow/environment-variable-loading.md)
- [Next.js: Startup Environment Variable Validation](/git-workflow/nextjs-env-validation.md)
