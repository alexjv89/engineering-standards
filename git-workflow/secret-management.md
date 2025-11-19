# Secret Management

Using secret managers for production secrets.

## Secret Manager Pattern

For production, use dedicated secret management:

```javascript
// config/secrets.js
import { SecretsManager } from 'aws-sdk';

const client = new SecretsManager({ region: 'us-east-1' });

export async function getSecret(name) {
  const result = await client.getSecretValue({ SecretId: name }).promise();
  return JSON.parse(result.SecretString);
}

// Usage
const dbCreds = await getSecret('production/database');
```

## When to Use Secret Managers

**Use for:**
- Production credentials
- Database passwords
- API keys
- OAuth secrets
- Encryption keys

**Don't use for:**
- Public API keys
- Non-sensitive configuration
- Feature flags
- Development defaults

## Environment-Specific Loading

```javascript
// config/env.js
import { getSecret } from './secrets';

export async function loadConfig() {
  if (process.env.NODE_ENV === 'production') {
    // Load from secret manager
    const secrets = await getSecret('app/production');
    return {
      database: { url: secrets.DATABASE_URL },
      api: { key: secrets.API_KEY },
    };
  } else {
    // Load from environment variables
    return {
      database: { url: process.env.DATABASE_URL },
      api: { key: process.env.API_KEY },
    };
  }
}
```

## Related Notes
- [Environment File Structure](/git-workflow/environment-file-structure.md) - File structure and .gitignore
- [Environment Variable Loading](/git-workflow/environment-variable-loading.md) - Loading and validation patterns
- [Background Tasks: Job Queues](/git-workflow/job-queue-pattern.md) - Redis URL configuration
