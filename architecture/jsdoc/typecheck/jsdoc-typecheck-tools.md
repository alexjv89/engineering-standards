# JSDoc: Type Checking Tools (VS Code vs CLI)

Two tools perform type checking on JavaScript with JSDoc: VS Code (real-time) and `tsc` CLI (batch).

## VS Code Type Checking

VS Code uses the TypeScript language server to check files in real-time:

- **Automatic**: No setup needed beyond jsconfig.json
- **Real-time**: Errors appear as you type
- **Per-file**: Respects `// @ts-check` directive
- **IDE integration**: Hover types, autocomplete, go-to-definition

## CLI Type Checking with tsc

Run type checking from the command line:

```json
{
  "scripts": {
    "typecheck": "tsc -p jsconfig.json",
    "typecheck:watch": "tsc -p jsconfig.json --watch"
  }
}
```

**Use cases**:
- CI/CD pipelines
- Pre-commit hooks
- Batch validation before deployment

## Ensuring Consistent Results

Both tools use the TypeScript compiler engine, but VS Code may use a different version than your project. To ensure identical behavior:

### Pin VS Code to Project TypeScript

Create `.vscode/settings.json`:

```json
{
  "typescript.tsdk": "node_modules/typescript/lib"
}
```

This tells VS Code to use the project's TypeScript instead of its bundled version.

### If Results Still Differ

1. Verify jsconfig.json is saved and valid JSON
2. Restart VS Code TypeScript server: `Cmd+Shift+P` > "TypeScript: Restart TS Server"
3. Check that `node_modules/typescript` exists (run `npm install`)

## CI Integration Example

```yaml
# GitHub Actions
- name: Type check
  run: npm run typecheck
```

The CLI exits with error code 1 if type errors exist, failing the build.

## Related Notes
- [Gradual Type Checking](/architecture/jsdoc/typecheck/jsdoc-gradual-typecheck.md)
- [JavaScript with JSDoc](/principles/javascript-with-jsdoc.md)
