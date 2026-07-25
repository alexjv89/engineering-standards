# Abstraction Must Earn Its Keep

Every layer of indirection — a helper, a constant, a wrapper, an extra file — is a thing the next reader must jump to before they understand the code. Add one only when it buys more than it costs. When it doesn't, inline it and delete the ceremony.

## The Principle

Indirection has a real, recurring price: to understand one line you must go read another. That price is worth paying when the indirection hides genuine complexity, removes *load-bearing* duplication, or names something that actually varies. It is not worth paying to relocate a value you could have read in place.

The same goes for **ceremony** — structure added for symmetry or "cleanliness" rather than need. A folder of fifteen one-line files, a constant used exactly once, an interface with a single implementation, a factory that only ever builds one thing. Each adds moving parts without adding meaning.

Default to the flattest thing that reads clearly. Reach for an abstraction when the code forces it, not in anticipation that it might.

## Why It Matters

- **Reading is the common case.** Code is read far more than it is changed. An abstraction optimises for the rare edit ("change it in one place") at the cost of every read ("jump away to see what this is"). For values that rarely change, that trade loses.
- **Indirection hides how much is really there.** One file of thin wrappers tells you honestly "this is simple." Fifteen files, an index, and a shared-constant module dress the same simplicity up as something that needs architecture.
- **Find-and-replace is cheap; comprehension is not.** "But if it changes I'd edit N places" assumes editing is the bottleneck. In one repo, a literal changed in nine call sites is a thirty-second grep. Attention spent decoding indirection is the scarce resource — see [JavaScript with JSDoc](/principles/javascript-with-jsdoc.md)'s fewer-moving-parts logic.
- **Premature abstraction guesses wrong.** Extracted before the second real use, it usually fits neither the first case nor the eventual one, and then it's load-bearing and awkward to undo.

## Anti-Pattern

```javascript
// BAD: a whole module + an import in every sibling file, to hold one string.
// resources/accounts/_base.js
export const BASE = '/api/v1/accounts';

// resources/accounts/get.js
import { BASE } from './_base.js';
export const get = (ctx, id) => ctx.get(`${BASE}/${encodeURIComponent(id)}`);

// resources/accounts/list.js
import { BASE } from './_base.js';
export const list = (ctx, params) => ctx.get(BASE, { searchParams: params });
// ...ten more one-line files, each importing BASE from the _base module.
```

To read `get`, you now detour through `_base.js` to learn `BASE` is just `'/api/v1/accounts'` — a value that changes about never. The folder-per-action split adds the same tax structurally: twelve files and an index to hold what is really one small object of wrappers.

## Correct Approach

```javascript
// GOOD: the path is a literal where it's used; one file holds the resource.
const unwrap = (r) => r?.data ?? null;   // earns its keep: shared behavior, de-noises 12 call sites

export function buildAccounts(ctx) {
  return {
    list:   (params = {}) => ctx.get('/api/v1/accounts', { searchParams: params }),
    get:    (id) => ctx.get(`/api/v1/accounts/${encodeURIComponent(id)}`).then(unwrap),
    create: (data) => ctx.post('/api/v1/accounts', data).then(unwrap),
    // ...
  };
}
```

Each line is readable on its own; the resource is one file you can take in at a glance. Note the contrast *within* the same code: `unwrap` stays, because it factors out real repeated **behavior** and materially cuts noise — that indirection earns its keep. The path constant went away, because it only relocated a literal.

## When Indirection Does Earn It

Keep the abstraction when at least one is true:

- **It hides genuine complexity** — auth refresh, pagination walking, ret/backoff. The wrapper is simpler to use than the thing it wraps.
- **The duplication is load-bearing** — the copies *must* change together for correctness, or the value genuinely varies (per-environment origin URL, a shared enum). Then one definition is correctness, not tidiness.
- **It factors out real behavior, reused** — like `unwrap` above, not a string used once.
- **The variation is real today** — you have two implementations now, not a hypothetical second one.

A single origin base URL read from config in one place earns centralising; a per-resource path literal does not. The test is whether the thing behind the indirection actually varies or actually repeats meaningfully.

## Smell Test

Before adding (or keeping) an abstraction, ask:

1. **"If I inlined this, would the code be harder to *understand*, or just less DRY?"** If only less-DRY, inline it.
2. **"Does this indirection hide something that varies, or just relocate a value I could read in place?"** Relocation isn't worth a jump.
3. **"How many real call sites, today?"** One use is not a pattern. Wait for the second.
4. **"Is this structure here because the code needs it, or because it looks symmetric?"** Symmetry is not a requirement.

## Related Notes

- [JavaScript with JSDoc](/principles/javascript-with-jsdoc.md) — the same minimize-moving-parts philosophy applied to tooling
- [No Backward Compatibility](/principles/no-backward-compatibility.md) — delete the old shape rather than keep a second path; fewer moving parts
- [No Hidden Side Effects](/principles/no-hidden-side-effects.md) — functions that do what their name says need no indirection to explain them
- [Functional Programming](/principles/functional-programming.md) — plain functions over structural ceremony
