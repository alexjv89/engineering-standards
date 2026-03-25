# EC2 Server Layout

Directory structure and process management on the deployment server.

## Directory Structure

```
/home/ubuntu/
├── orchestrator.finopsbricks.com/           # Production
│   ├── .next/                               # Build artifacts (rsynced)
│   ├── public/                              # Static assets (rsynced)
│   ├── .env.local                           # Runtime environment variables
│   └── ...                                  # Source code (git managed)
├── staging-orchestrator.finopsbricks.com/   # Staging
│   ├── .next/
│   ├── public/
│   ├── .env.local
│   └── ...
```

Convention: staging directories are prefixed with `staging-`.

## PM2 Processes

| Process Name | Port | Directory |
|-------------|------|-----------|
| `orchestrator.finopsbricks.com` | 4005 | `~/orchestrator.finopsbricks.com/` |
| `staging-orchestrator.finopsbricks.com` | 4006 | `~/staging-orchestrator.finopsbricks.com/` |

## File Ownership

All files in the deploy directories **must be owned by the deploy user** (`ubuntu`), not `root`.

```bash
# Check ownership
ls -la ~/staging-orchestrator.finopsbricks.com/.env.local

# Fix if owned by root
sudo chown ubuntu:ubuntu ~/staging-orchestrator.finopsbricks.com/.env.local
```

If files are owned by `root`, the deploy will fail with `Permission denied` when trying to write `.env.local`.

## Useful PM2 Commands

```bash
pm2 status                                              # List all processes
pm2 logs orchestrator.finopsbricks.com                  # View logs
pm2 restart orchestrator.finopsbricks.com               # Restart
pm2 describe orchestrator.finopsbricks.com              # Detailed info
pm2 save                                                # Persist process list
```

## Related Notes

- [Deploy Pipeline](/deployment/deploy-pipeline.md)
- [EC2 Troubleshooting](/deployment/ec2-troubleshooting.md)
