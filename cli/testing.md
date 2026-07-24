# Testing

Test handlers as units: mock the client, invoke the handler, assert on stdout, stderr, and exit
code. Don't hit the network; don't shell out to the built binary.

## Harness: `captureOutput()`

Copy this helper from a sibling wrapper (`finopsbricks/cli/cli-fob/tests/cli/helpers.js`). It
spies on `console.log`/`console.error` and makes `process.exit` throw an `ExitError`, so a
handler that exits halts and the test can assert on the code and captured output:

```js
export class ExitError extends Error {
  constructor(code) { super(`process.exit called with code ${code}`); this.code = code; }
}

export function captureOutput() {
  const lines = { stdout: [], stderr: [] };
  const spies = [
    jest.spyOn(console, 'log').mockImplementation((...a) => lines.stdout.push(a.join(' '))),
    jest.spyOn(console, 'error').mockImplementation((...a) => lines.stderr.push(a.join(' '))),
    jest.spyOn(process, 'exit').mockImplementation((code) => { throw new ExitError(code ?? 0); }),
  ];
  return {
    get stdout() { return lines.stdout.join('\n'); },
    get stderr() { return lines.stderr.join('\n'); },
    restore() { spies.forEach((s) => s.mockRestore()); },
  };
}
```

## Mock the client, not the network

Mock the transport/client module so no real request goes out. With Jest ESM
(`node --experimental-vm-modules`), use `jest.unstable_mockModule` **before** importing the
handler:

```js
jest.unstable_mockModule('../../../src/utils/http.js', () => ({
  apiGet: jest.fn(),
  apiPost: jest.fn(),
  ApiError: MockApiError,
}));
const { apiGet } = await import('../../../src/utils/http.js');
const { showAccountHandler } = await import('../../../src/cli/statements/accounts/show.js');
```

## What to assert

```js
describe('showAccountHandler', () => {
  let out;
  beforeEach(() => { jest.clearAllMocks(); out = captureOutput(); });
  afterEach(() => out.restore());

  it('formats the account', async () => {
    apiGet.mockResolvedValue({ data: { id: '42', name: 'Acme' } });
    await showAccountHandler({ id: '42' });
    expect(apiGet).toHaveBeenCalledWith('/api/v1/accounts/42');  // right call
    expect(out.stdout).toContain('Acme');                        // right output
  });

  it('exits 1 when not found', async () => {
    apiGet.mockResolvedValue({ data: null });
    await expect(showAccountHandler({ id: 'x' })).rejects.toThrow(ExitError);
    expect(out.stderr).toContain('No account found');
  });
});
```

Cover, per handler: the correct client call (path + params), the formatted output, the `--json`
branch, and the error/exit path. Test pure client functions (pagination, param building)
directly, without the CLI.

## Runner

Match the existing wrappers: Jest with `--experimental-vm-modules` for ESM. A minimal
protocol-only wrapper may use the built-in `node --test`, but keep the mock-the-client,
assert-output approach either way.

## Related Notes

- [Error Handling](./error-handling.md) — the `safe()`/exit-code contract under test
- [Project Structure](./project-structure.md) — client vs handler boundary that makes this mockable
