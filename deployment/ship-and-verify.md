# Ship and Verify

A change is **done** when it is running in production and has been checked there, not when it's pushed. Deploys only happen through CI/CD.

## Done Means

1. **Pushed** to the branch the user works on.
2. **CI green** for that exact commit (lint, typecheck, test, storybook test).
3. **Deploy green** for that commit (`deploy-main.yml` runs after CI via `workflow_run`).
4. **Production smoke check** passes:
   - public pages load with no page errors (mobile WebKit + desktop Chromium)
   - assets the change added return `200`
   - for UI changes: the changed page renders in production

Report "done" only after step 4. If you stop earlier, say exactly which step you stopped at.

```bash
sha=$(git rev-parse HEAD)
gh run list --commit "$sha"                  # CI + Deploy for this commit
gh run watch <run-id> --exit-status          # wait for each
gh run view <run-id> --log-failed            # on failure
```

## When a Deploy Fails

- Read the failed log. Fix forward with a commit that goes through CI again.
- **Never** SSH to the server to move files, pull, restart or rebuild code, and never suggest it as a way around the pipeline. Code reaches the server only through CI/CD.
- If the fix needs a workflow change, that is a normal commit, reviewed like any other.
- If the cause is server state the pipeline can't fix (e.g. a root-owned `.env.local`, see [EC2 Troubleshooting](/deployment/ec2-troubleshooting.md)), stop and report it to a person with the log line. Server administration is their call.
- A failed deploy can leave production half-updated (new `.next/` rsynced, old process running), so treat it as an outage and say so immediately.

## Related Notes

- [Deploy Pipeline](/deployment/deploy-pipeline.md)
- [CI Pipeline](/deployment/ci-pipeline.md)
- [EC2 Troubleshooting](/deployment/ec2-troubleshooting.md)
