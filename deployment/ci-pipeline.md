# CI Pipeline

GitHub Actions CI workflow that runs on every push and pull request.

## Workflow File

`.github/workflows/ci.yml`

## Trigger

```yaml
on:
  push:
  pull_request:
  workflow_dispatch:
```

## Pipeline Steps

1. **Setup - Detect Changes** — Uses `dorny/paths-filter` to determine which areas changed (`components`, `app`, `core`)
2. **Check - Lint** — Always runs. `npm run lint`
3. **Check - Types** — Always runs. `npm run typecheck`
4. **Test - Unit** — Conditional. Runs if `core`, `app`, or `components` changed
5. **Test - Storybook** — Conditional. Runs if `components` or `app` changed

## Change Detection

```yaml
filters: |
  components:
    - 'src/components/**'
    - 'stories/**'
    - '.storybook/**'
  app:
    - 'src/app/**'
  core:
    - 'src/lib/**'
    - 'src/utils/**'
    - 'src/database/**'
    - 'src/jobs/**'
```

Checks (lint, typecheck) always run. Tests only run when relevant files change.

## Downstream

Deploy workflows (`deploy-main.yml`, `deploy-develop.yml`) trigger via `workflow_run` after CI completes successfully.

## Related Notes

- [Deploy Pipeline](/deployment/deploy-pipeline.md)
- [GitHub Secrets](/deployment/github-secrets.md)
