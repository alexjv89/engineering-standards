# Environment Variable Loading

Pattern for loading and validating environment variables.

## Loading Pattern

```javascript
// config/env.js
const required = [
  'DATABASE_URL',
  'API_KEY',
  'AUTH_SECRET',
];

// Validate required variables
for (const key of required) {
  if (!process.env[key]) {
    throw new Error(`Missing required environment variable: ${key}`);
  }
}

export const config = {
  database: {
    url: process.env.DATABASE_URL,
  },
  api: {
    key: process.env.API_KEY,
    timeout: parseInt(process.env.API_TIMEOUT || '10000'),
  },
  auth: {
    secret: process.env.AUTH_SECRET,
    jwtExpiry: process.env.JWT_EXPIRY || '7d',
  },
};
```

## Validation Strategy

1. **List required variables** at the top of config file
2. **Validate on startup** - fail fast if missing
3. **Provide defaults** for optional variables
4. **Type conversion** - parse integers, booleans as needed

## Type Conversion

```javascript
// Integer
const timeout = parseInt(process.env.API_TIMEOUT || '10000');

// Boolean
const enableFeature = process.env.ENABLE_BETA === 'true';

// Array
const allowedOrigins = process.env.CORS_ORIGINS?.split(',') || [];
```

## Related Notes
- [Environment File Structure](/git-workflow/environment-file-structure.md) - File structure and .gitignore
- [Secret Management](/git-workflow/secret-management.md) - Security and secret manager integration
- [Constants Naming](/naming/constants-screaming-snake.md) - Environment variable naming
