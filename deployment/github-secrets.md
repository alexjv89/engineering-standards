# GitHub Secrets

Required secrets for CI/CD pipelines and how they're used.

## Required Secrets

| Secret | Used By | Purpose |
|--------|---------|---------|
| `EC2_SSH_KEY` | Deploy | SSH private key for EC2 access |
| `HOST_DNS` | Deploy | EC2 hostname |
| `USERNAME` | Deploy | SSH user (e.g., `ubuntu`) |
| `ENV_VARIABLES` | Deploy (prod) | Full `.env.local` contents for production |
| `STAGING_ENV_VARIABLES` | Deploy (staging) | Full `.env.local` contents for staging |
| `SENTRY_AUTH_TOKEN` | Deploy | Sentry source map upload during build |

## Write-Only

GitHub secrets **cannot be viewed after saving**. You can only overwrite them. If you suspect a secret has bad content, re-paste it.

## How Secrets Are Used

Secrets are used in two places during deploy:

1. **Build-time** (on GitHub Actions runner):
   ```yaml
   - name: Create .env file
     run: echo "${{ secrets.ENV_VARIABLES }}" > .env.local
   ```

2. **Runtime** (on EC2 via SSH):
   ```yaml
   ssh ... '
     echo "${{ secrets.ENV_VARIABLES }}" > .env.local &&
     ...
   '
   ```

Currently the same secret is used for both. Future plan: separate build-time and runtime secrets.

## Quoting Gotcha

In the deploy workflow, secrets are expanded inside a **single-quoted SSH body**. GitHub Actions `${{ }}` expressions are expanded before the shell runs. If the secret value contains `'` (single quote), it will break the SSH command. Avoid single quotes in secret values.

## Related Notes

- [Deploy Pipeline](/deployment/deploy-pipeline.md)
- [Environment File Structure](/git-workflow/environment-file-structure.md)
- [Secret Management](/git-workflow/secret-management.md)
