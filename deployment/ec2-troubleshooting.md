# EC2 Troubleshooting

Common deployment failures and how to fix them.

## "Permission denied" on `.env.local`

**Symptom**: Deploy fails with `bash: line N: .env.local: Permission denied`

**Cause**: `.env.local` on the server is owned by `root` instead of the deploy user.

**Fix**:
```bash
ssh your-server
sudo chown ubuntu:ubuntu ~/staging-orchestrator.finopsbricks.com/.env.local
# or for production:
sudo chown ubuntu:ubuntu ~/orchestrator.finopsbricks.com/.env.local
```

**How it happens**: Someone ran a command with `sudo` that wrote to `.env.local`, changing ownership to `root`.

## PM2 Process Not Found

**Symptom**: Deploy log shows "Process not found, starting fresh..."

**This is normal** on first deploy. PM2 will start the process. On subsequent deploys it restarts the existing process.

## Verify a Deployment

```bash
# Check PM2 status
pm2 status

# Check if the process is running and uptime reset
pm2 describe orchestrator.finopsbricks.com | grep uptime

# Check recent logs for errors
pm2 logs orchestrator.finopsbricks.com --lines 20

# Verify .next folder was synced (check timestamp)
ls -la ~/orchestrator.finopsbricks.com/.next/static/chunks/ | head -5
```

## Rsync Succeeded but SSH Step Failed

The deploy step runs rsync first, then SSH. If rsync succeeds but the SSH command fails, check:

1. **Which line failed** — The error shows `bash: line N:` which helps locate the failing command
2. **File ownership** — Most common cause (see above)
3. **npm ci failure** — Check if Node version on server matches the workflow (`node -v`)
4. **git pull conflict** — Check if there are uncommitted changes on the server

## Node Version Mismatch

Server runs Node v20.18.0. Some packages warn about requiring >= 20.19.0 (`EBADENGINE`). These are warnings, not errors.

## Related Notes

- [EC2 Server Layout](/deployment/ec2-server-layout.md)
- [Deploy Pipeline](/deployment/deploy-pipeline.md)
- [GitHub Secrets](/deployment/github-secrets.md)
