# Next.js: Startup Environment Variable Validation

Validate required environment variables in `instrumentation.js` so the server
refuses to start if any are missing.

## Pattern

```javascript
// src/instrumentation.js
const required_env_vars = [
  'DATABASE_URL',
  'AUTH_SERVICE_URL',
  'INTERNAL_API_SECRET',
];

export async function register() {
  if (process.env.NEXT_RUNTIME === 'nodejs') {
    for (const key of required_env_vars) {
      if (!process.env[key]) {
        throw new Error(`Missing required environment variable: ${key}`);
      }
    }

    // ... rest of server startup
  }
}
```

## Why `instrumentation.js`, Not `next.config.mjs`

`next.config.mjs` is loaded by every Next.js CLI command — `next lint`, `next build`,
and Storybook — not just server startup. Putting validation there breaks CI jobs
that don't need runtime env vars.

`instrumentation.js` is Next.js's dedicated server startup hook. The
`NEXT_RUNTIME === 'nodejs'` guard ensures validation only runs when the actual
server process starts.

| Context | `next.config.mjs` runs | `instrumentation.js` runs |
|---|---|---|
| `next dev` / `next start` | ✅ | ✅ |
| `next build` | ✅ | ❌ |
| `next lint` | ✅ | ❌ |
| Storybook | ✅ | ❌ |

## Trade-off

A `next build` with missing vars will succeed — the failure happens at runtime,
not build time. This is acceptable: the server still fails fast on startup with
a clear error message.

## What to Include

Only vars the **app server** reads at runtime. Do not include:
- Build-time vars (e.g. `SENTRY_AUTH_TOKEN`) — not present during `next dev`
- Script-only vars — not the server's concern

## Related Notes

- [Environment Variable Loading](/git-workflow/environment-variable-loading.md)
- [Environment File Structure](/git-workflow/environment-file-structure.md)
- [.env.example: Section Structure](/git-workflow/env-example-sections.md)
- [Config Object Anti-pattern](/git-workflow/config-object-anti-pattern.md)
