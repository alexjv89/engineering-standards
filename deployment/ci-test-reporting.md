# CI Test Reporting

Publish JUnit test results as GitHub Actions job summaries so pass/fail/skip counts are visible without opening logs.

## Components

1. **`jest-junit`** — Jest reporter that writes `junit.xml` alongside the default console output.
2. **`dorny/test-reporter`** — GitHub Action that reads `junit.xml` after the test step and renders a summary card (e.g. "2020 passed, 10 skipped") plus a check run.

## Jest Config

Add `jest-junit` as a second reporter (keep `default` for local console output):

```js
reporters: [
  'default',
  'jest-junit'
],
```

No reporter options needed — defaults write to `./junit.xml`, which is what `dorny/test-reporter` expects.

## Dependencies

```
npm install --save-dev jest-junit
```

## Workflow Permissions

`dorny/test-reporter` needs elevated permissions to create check runs — without `checks: write`, reports silently fail to appear, especially on `workflow_dispatch` runs:

```yaml
permissions:
  contents: read
  checks: write
  pull-requests: write
```

## Workflow Step

Add immediately after the test step, with `if: always()` so results still publish when tests fail:

```yaml
- run: npm test
- name: Publish test results
  uses: dorny/test-reporter@v1
  if: always()
  with:
    name: "Results - Unit"
    path: junit.xml
    reporter: jest-junit
```

Give each test job's reporter step a distinct `name` (e.g. `Results - Unit`, `Results - Parser`) so multiple summaries don't collide when a workflow runs several test jobs.

## Gotchas

- `junit.xml` is a build artifact — add it to `.gitignore`, never commit it.
- The reporter step must run even on failure (`if: always()`); otherwise a failing test run shows no summary at all, which is the case you most want visibility into.

## Related Notes

- [CI Pipeline](/deployment/ci-pipeline.md)
- [GitHub Secrets](/deployment/github-secrets.md)
