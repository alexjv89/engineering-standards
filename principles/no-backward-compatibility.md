# No Backward Compatibility

For an early-stage, single-developer product, backward compatibility is a liability, not a courtesy. When you change a contract — a field name, an enum, a route, a function signature — change it everywhere and delete the old shape. Do not leave aliases, shims, or dual-read paths behind.

## The Principle

Every compatibility layer is permanent code you now maintain forever: a second code path to read, test, reason about, and keep in sync. For a large product with external consumers under contract, that cost buys something real. For a one-person team with no external API commitments, it buys nothing — you are paying legacy tax to protect callers that are all in your own repos and all editable in the same commit.

So the default is a **hard refactor**: migrate the data, rename the identifier, update every call site, and remove the old one — in one change. "Done" means the old shape does not exist, not that it still works.

## Why It Matters

- **One code path.** Every reader — human or LLM — sees exactly one way things work. No "which of these two is current?"
- **No residue to re-infect.** Aliases are exactly the surviving artifacts that [Propagation Risk](/principles/propagation-risk.md) warns about: the next reader pattern-matches the old name and reproduces it.
- **Cheap because you control all callers.** In a monorepo with a single dev, "update every call site" is a grep and an afternoon, not a coordination problem. The thing that makes back-compat necessary elsewhere — callers you can't reach — doesn't exist here.
- **Compounding maintainability.** The team's scarcest resource is attention, not migration effort. Two code paths cost attention on every future read; the migration costs effort once.

## Anti-Pattern

```javascript
// BAD: renamed is_archived -> status enum, but kept the old flag alive "just in case"
status: { type: DataTypes.ENUM('active', 'retired', 'quarantined') },
get is_archived() { return this.status !== 'active'; },   // deprecated alias
// ...and every report now reads status, but three old callers still read is_archived
```

Now two vocabularies describe one fact, forever. The alias never gets removed because nothing forces the issue.

## Correct Approach

```javascript
// GOOD: status is the only representation. is_archived is gone.
status: { type: DataTypes.ENUM('active', 'retired', 'quarantined') },
// migration backfills every row; every call site updated in the same change; is_archived deleted.
```

Migrate the data, update all callers, delete the old field. `git grep is_archived` returns nothing but sealed commit history.

## The Genuine Boundary

The only place compat is warranted is a contract you truly do not control: a published package consumers have installed, a public URL, a webhook payload an external system already parses. That boundary is narrow and, at this stage, usually empty. If you're unsure whether a caller is external, it isn't — everything is in your repos. See [Propagation Risk → When Residue Is Genuinely Unavoidable](/principles/propagation-risk.md) for how to isolate the rare real case behind a single wrapper.

## Smell Test

Before writing a compatibility shim, ask: **"Who calls the old shape that I cannot edit in this same change?"** If the answer is "no one" — and for internal code it almost always is — there is nothing to be compatible with. Delete the old shape.

## Related Notes

- [Propagation Risk](/principles/propagation-risk.md) — retired terms left as residue re-infect new contexts
- [No Hidden Side Effects](/principles/no-hidden-side-effects.md)
- [JavaScript with JSDoc](/principles/javascript-with-jsdoc.md) — same fewer-moving-parts philosophy
