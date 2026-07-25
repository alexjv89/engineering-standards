# No Ambient Configuration

Reusable code takes its configuration — credentials, endpoints, options — as explicit arguments. It does not reach into `process.env` (or any global) to find them, and it does not dictate what the caller's environment variables are named. Only the outermost application layer reads the environment, and it *injects* what it read.

## The Principle

When a library reads `process.env.FOB_STM_API_KEY` inside itself, it takes on **ambient authority**: its behavior depends on hidden global state the caller can't see at the call site. Two harms follow:

1. **It becomes impure** — same call, different result depending on environment set far away. You can't read a call site and know what it does; you can't test it without mutating global state. This is the reading-side twin of [No Hidden Side Effects](/principles/no-hidden-side-effects.md): a hidden *input* is as dishonest as a hidden output.
2. **It imposes a naming convention** — the library now dictates that *your* env vars must be called `FOB_STM_API_*`. Every consumer inherits a convention they didn't choose, and multi-tenant use (two orgs, two credential sets in one process) turns into a fight against a global singleton.

The fix is **dependency injection**: pass configuration in. The library stays a pure function of its arguments. The **application at the edge** — a CLI's config resolver, a worker's entry point — is the one place that reads the environment, and it hands the values to the library explicitly.

## Anti-Pattern

```javascript
// BAD: the library reaches into the environment and names your vars for you.
export function fobStm(credentials) {
  const api_key = credentials?.api_key ?? process.env.FOB_STM_API_KEY;       // ambient
  const api_secret = credentials?.api_secret ?? process.env.FOB_STM_API_SECRET; // ambient
  // ...
}

// Caller:
const stm = fobStm();   // reads WHAT, from WHERE? invisible. and it dictated the env names.
```

`fobStm()` "works" or fails based on env set elsewhere — action at a distance. A stale `FOB_STM_API_KEY` in a shell silently changes which org you hit.

## Correct Approach

```javascript
// GOOD: the library is a pure function of its arguments. No env reads. No imposed names.
// api_key / api_secret are required; api_url has a sensible default the caller may override.
export function fobStm({ api_key, api_secret, api_url = 'https://statements.finopsbricks.com' }) {
  if (!api_key || !api_secret) throw new Error('fobStm: api_key and api_secret are required');
  // ...
}

// The APP at the edge reads the environment (its own names, its choice) and injects:
const stm = fobStm({
  api_key: process.env.FOB_STM_API_KEY,       // the worker picked this name
  api_secret: process.env.FOB_STM_API_SECRET,
});

// Multi-tenant falls out for free — no global to fight:
const orgA = fobStm({ api_key: process.env.KEY_A, api_secret: process.env.SEC_A });
const orgB = fobStm({ api_key: process.env.KEY_B, api_secret: process.env.SEC_B });
```

## The Boundary — where reading the environment *is* allowed

Something has to read `process.env`; the rule is **where**. The outermost layer of an application — a CLI's credential resolver, a worker's client-builder helper — reads the environment once and injects. Libraries never do. A CLI backed by a config file should resolve from **that** (a named profile, a `--profile` flag), not a competing magic env convention: an env var that silently overrides the active profile is the same action-at-a-distance footgun. If config-less or CI use needs credentials without a config file, prefer **explicit flags** (`--api-key`) over magic env names — explicit beats ambient even at the edge.

## Smell Test

- **"Does this function read `process.env` (or a global) for something a caller could pass?"** If yes, and it isn't the app's outermost edge, inject it instead.
- **"Does this code dictate an environment variable *name* to its callers?"** A library naming your env vars is a convention imposed, not chosen.
- **"Can I construct two of these with different credentials in one process?"** If a global singleton makes that awkward, the config is ambient, not injected.
- **"Can I test this without setting environment variables?"** If not, the dependency is hidden.

## Related Notes

- [No Hidden Side Effects](/principles/no-hidden-side-effects.md) — a hidden input is the twin of a hidden output; both break "reason from the call site"
- [Functional Programming](/principles/functional-programming.md) — a function of its arguments is pure and testable; reading globals breaks referential transparency
- [Abstraction Must Earn Its Keep](/principles/abstraction-earns-its-keep.md) — an imposed env convention is unearned coupling
