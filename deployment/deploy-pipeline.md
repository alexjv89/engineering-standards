# Deploy Pipeline

GitHub Actions deploy workflows that build on the runner and deploy to EC2 via rsync + SSH.

## Workflow Files

Naming convention: `deploy-<branch>.yml`

| File | Name | Branch | Target |
|------|------|--------|--------|
| `deploy-main.yml` | Deploy | `main` | Production |
| `deploy-develop.yml` | Deploy Staging | `develop` | Staging |

## Trigger

Both workflows trigger after CI passes on their respective branch:

```yaml
on:
  workflow_run:
    workflows: ["CI"]
    types: [completed]
    branches: [main]  # or [develop] for staging
  workflow_dispatch:
```

The `workflow_dispatch` trigger allows manual re-runs from the GitHub Actions UI.

## Deploy Steps

1. **Checkout + Setup Node** — Check out code, set up Node 20
2. **Install dependencies** — `npm ci` on the runner
3. **Create .env file** — Write GitHub secret to `.env.local` for build-time env vars
4. **Build** — `npm run build` (Next.js build on the runner)
5. **Verify build artifacts** — Confirm `.next/` folder exists
6. **Deploy to EC2** via SSH:
   - Rsync `.next/` and `public/` to the server (with `--delete`)
   - SSH into server:
     - `git pull` to sync source code
     - `npm ci` to install dependencies
     - Write runtime `.env.local` from GitHub secret
     - Restart PM2 process (or start fresh if not found)
     - `pm2 save` to persist process list

## Key Design Decisions

- **Build on runner, not on EC2** — Avoids memory/CPU pressure on the server
- **Rsync build artifacts** — Only `.next/` and `public/` are synced; source code comes from `git pull`
- **Same secret for build and runtime env vars** — Future plan to separate these

## Related Notes

- [CI Pipeline](/deployment/ci-pipeline.md)
- [EC2 Server Layout](/deployment/ec2-server-layout.md)
- [GitHub Secrets](/deployment/github-secrets.md)
