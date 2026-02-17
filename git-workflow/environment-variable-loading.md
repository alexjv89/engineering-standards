# Environment Variable Loading

Pattern for loading and validating environment variables.

## Core Principle

**Use `process.env` directly. Do not create config objects that mirror environment variables.**

The `.env.example` file documents defaults. Code should fail fast if required variables are missing.

## Validation Pattern

```javascript
// At application startup (e.g., top of main entry file)
import 'dotenv/config';

const required = [
  'DATABASE_URL',
  'API_KEY',
  'AUTH_SECRET',
];

for (const key of required) {
  if (!process.env[key]) {
    console.error(`Missing required environment variable: ${key}`);
    process.exit(1);
  }
}
```

## Usage Pattern

```javascript
// Anywhere in the codebase - use process.env directly
const response = await fetch(process.env.API_URL, {
  headers: { 'Authorization': `Bearer ${process.env.API_KEY}` }
});

// For integers, parse at point of use
const timeout = parseInt(process.env.API_TIMEOUT);
```

## Default Values

**Never use `process.env.X || 'default'` in code.**

Defaults belong in `.env.example`:

```bash
# .env.example
DATABASE_URL=postgresql://localhost:5432/myapp_dev
API_TIMEOUT=10000
LOG_LEVEL=info
```

Developers copy `.env.example` to `.env` and modify as needed. This makes defaults discoverable and keeps code simple.

## Type Conversion

When you need non-string types, parse at point of use:

```javascript
// Integer - parse when needed
const timeout = parseInt(process.env.API_TIMEOUT);

// Boolean - explicit check
const isEnabled = process.env.ENABLE_BETA === 'true';

// Array - split when needed
const origins = process.env.CORS_ORIGINS?.split(',') || [];
```

## Anti-patterns

See [Config Object Anti-pattern](./config-object-anti-pattern.md) for patterns to avoid.

## Related Notes

- [Environment File Structure](./environment-file-structure.md) - File structure and .gitignore
- [Secret Management](./secret-management.md) - Security and secret manager integration
- [Config Object Anti-pattern](./config-object-anti-pattern.md) - Why not to wrap env vars
