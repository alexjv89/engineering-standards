# Config Object Anti-pattern

Avoid creating config objects that simply mirror environment variables.

## The Anti-pattern

```javascript
// BAD: config.js that just reassigns env vars
export const config = {
  orchestrator_url: process.env.ORCHESTRATOR_URL || 'http://localhost:3000',
  worker_secret: process.env.WORKER_SECRET,
  poll_interval_ms: parseInt(process.env.POLL_INTERVAL_MS || '2000'),
  api_url: process.env.API_URL,
  api_key: process.env.API_KEY,
};

// Then used as:
fetch(config.api_url, { headers: { 'Authorization': config.api_key } });
```

## Why It's Bad

1. **Adds indirection** - Now you need to check config.js to understand what env var maps to what
2. **Hides defaults** - Defaults buried in code instead of `.env.example`
3. **No real benefit** - Just renaming `process.env.API_URL` to `config.api_url`
4. **False sense of encapsulation** - The config object doesn't provide any actual abstraction

## The Better Pattern

```javascript
// GOOD: Use process.env directly
fetch(process.env.API_URL, {
  headers: { 'Authorization': process.env.API_KEY }
});
```

```bash
# .env.example - defaults documented here
ORCHESTRATOR_URL=http://localhost:3000
POLL_INTERVAL_MS=2000
API_URL=
API_KEY=
```

## When Config Objects Are Appropriate

Config objects make sense when they provide actual value:

1. **Computed values** - Derived from multiple env vars
2. **Environment-specific logic** - Different behavior in dev/prod
3. **External configuration** - Loading from files, APIs, or secrets manager

```javascript
// GOOD: Config object that provides actual value
export const dbConfig = {
  // Computed from multiple env vars
  connectionString: `postgresql://${process.env.DB_USER}:${process.env.DB_PASS}@${process.env.DB_HOST}:${process.env.DB_PORT}/${process.env.DB_NAME}`,

  // Environment-specific
  pool: process.env.NODE_ENV === 'production'
    ? { min: 5, max: 20 }
    : { min: 1, max: 5 },
};
```

## Default Values Anti-pattern

**Never use `process.env.X || 'default'` in code.**

```javascript
// BAD: Default in code
const timeout = process.env.TIMEOUT || '5000';

// BAD: Default in config object
export const config = {
  timeout: process.env.TIMEOUT || '5000',
};
```

Defaults belong in `.env.example` where they are:
- Discoverable by developers
- Version controlled
- Documented in one place

## Related Notes

- [Environment Variable Loading](./environment-variable-loading.md) - Correct pattern
- [Environment File Structure](./environment-file-structure.md) - .env.example usage
