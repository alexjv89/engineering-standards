# Next.js: NEXT_PUBLIC_ Environment Variable Naming

When a variable exists in both server-side and browser-side forms, share the same
base name — differing only by the `NEXT_PUBLIC_` prefix.

## Pattern

```bash
# Server-side (can be an internal hostname, e.g. Docker network)
AUTH_SERVICE_URL=http://auth-service:5001

# Browser-side (must be the public-facing URL)
NEXT_PUBLIC_AUTH_SERVICE_URL=https://auth.example.com
```

## Why

`NEXT_PUBLIC_` already communicates "exposed to the browser." The base name
identifies the service. Giving them different names (e.g. `AUTH_SERVICE_URL` and
`NEXT_PUBLIC_AUTH_URL`) obscures the relationship — a developer reading either
one cannot easily find its counterpart.

## Which Files Use Which

| Runtime | Should use |
|---|---|
| `"use client"` components | `NEXT_PUBLIC_AUTH_SERVICE_URL` |
| Server components, API routes, `server-only` files | `AUTH_SERVICE_URL` |
| Middleware (Edge runtime) | `AUTH_SERVICE_URL` |

The `NEXT_PUBLIC_` form is only necessary where code runs in the browser.
All server-side code should use the non-prefixed form, even if the values
happen to be the same in a given environment.

## Why Two Variables

In production, the values often differ:
- Server calls go to an internal hostname (faster, no SSL termination overhead)
- Browser calls go to the public URL

Having two vars keeps this distinction available even when local dev uses the
same value for both.

## Related Notes

- [Environment Variable Loading](/git-workflow/environment-variable-loading.md)
- [Environment File Structure](/git-workflow/environment-file-structure.md)
- [Next.js: Startup Environment Variable Validation](/git-workflow/nextjs-env-validation.md)
