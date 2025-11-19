# Environment File Structure

File structure and naming conventions for environment variables.

## File Structure

```
.env.local          # Local development (not committed)
.env.development    # Development defaults (committed)
.env.staging        # Staging configuration (committed)
.env.production     # Production configuration (committed, no secrets)
.env.example        # Template (committed)
```

## .env.example Template

```bash
# API Configuration
API_BASE_URL=https://api.example.com
API_TIMEOUT=10000

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/dbname

# Authentication
AUTH_SECRET=generate-random-secret-here
JWT_EXPIRY=7d

# External Services
STRIPE_PUBLIC_KEY=pk_test_xxxxx
STRIPE_SECRET_KEY=sk_test_xxxxx

# Feature Flags
ENABLE_BETA_FEATURES=false
```

## .gitignore

Always ignore local environment files:

```
.env.local
.env*.local
```

## What to Commit

**Don't commit:**
- API keys
- Database passwords
- Private keys
- OAuth secrets

**Do commit:**
- Public API keys (clearly marked)
- Configuration structure
- Default values
- Feature flags

## Related Notes
- [Environment Variable Loading](/git-workflow/environment-variable-loading.md) - Loading and validation patterns
- [Secret Management](/git-workflow/secret-management.md) - Security and secret manager integration
- [Constants Naming](/naming/constants-screaming-snake.md) - Environment variable naming
