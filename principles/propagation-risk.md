# Propagation Risk

When you retire a term, identifier, or pattern, every surviving instance — even a "legitimate" filename, a frozen changelog entry, or a fenced code sketch — is a re-infection vector. Carve-outs compound. Rename completely or remove.

## The Principle

A migration from term A to term B isn't done when the *active* code uses B. It's done when no reader can encounter A and reproduce it as live vocabulary.

The mechanism:

1. A retired artifact survives somewhere (a filename, a code identifier, a marker string, a fenced API sketch, a changelog entry, a deprecated WIP).
2. A future reader — human or LLM — loads the artifact into working context.
3. The reader does **not** maintain a "this is residue, translate to current" flag. They pattern-match on the vocab and reproduce it in new prose.
4. The reproduction becomes a new artifact. The retired term has gained present-tense surface area.

Over time, the retired term can have *more* live presence than its replacement, because each carve-out is a permanent reservoir that re-seeds the term in every fresh reading.

## Why Context Complexity Compounds

Each surviving artifact requires future readers to maintain a mental translation. The cost is invisible per-instance but compounds:

- Every new doc written under the influence of residue multiplies the surface area.
- Every grep result returns both vocab generations, leaving the reader to disambiguate.
- LLM sessions flatten context — they treat all in-context tokens as candidate output vocab, with no privileged "this is quoted external" channel.
- "Done" becomes ambiguous: readers can't tell whether the residue is intentional or just missed.

Legacy doesn't fade; it amplifies.

## Anti-Pattern: Justified Carve-Outs

These exclusions feel prudent in the moment. Each one is a permanent reservoir.

- **"It's history"** — changelog or commit-message prose that uses the retired term as live vocabulary instead of a quoted historical name. The *date* is the historical marker. The vocabulary should be current.
- **"It's a real filename in another repo"** — leaving `old-name.md` as a cross-repo reference. The filename appears in prose; the next reader's prose reproduces the old name in a fresh heading.
- **"It's a code sketch in a deferred WIP"** — fenced `processOldName({ ... })` calls in an unbuilt API design. Future implementers treat the sketch as the proposed contract; the retired term gets baked into what eventually ships.
- **"That doc is in transition"** — WIP files using pre-sweep vocabulary while a sibling WIP completes the rename. The transition window is the moment of *maximum* re-infection — new docs are being written against the old vocab right now.

The common shape: the exclusion is justified by the reader's *intended* interpretation of the residue (it's history, it's external, it's a sketch). Readers don't carry that interpretation forward.

## Correct Approach

- **Rename at the artifact level.** Rename the file. Rename the identifier. Rename the marker string. Update fenced code. Rewrite changelog prose to current vocab — keep the date as the historical anchor.
- **Chase references across repos.** A grep that returns "one match, and it's the known carve-out" is the danger signal, not the all-clear. That single match will re-propagate.
- **Remove rather than translate.** If an artifact only existed to support the retired concept and can't be cleanly renamed, delete it.
- **Source-of-truth first, references second.** Rename the canonical file before updating inbound references, so the dependency direction stays clean.

## When Residue Is Genuinely Unavoidable

Some artifacts you cannot change:

- Published package names, public URLs, customer-facing identifiers under contract.
- External APIs you've committed to keep stable.
- Database column names or wire-protocol fields with live deployed consumers.

For those:

- **Isolate behind a current-name wrapper** so most code paths never see the legacy name.
- **Add an explicit translation note at the residue site**: `// Legacy name "<old>" retained for backward compat — see <current-location> for current vocab.`
- **Track the residue** in a single index (e.g. a `LEGACY-NAMES.md`) so it's visible and removable when the external constraint lifts.

The wrapper plus the note tell the reader (and the LLM): translate at this boundary, don't propagate.

## Smell Test

After a rename, ask: **"If a fresh reader sees only the surviving residue, would they reproduce the old term?"** If yes, the rename isn't done.

A stricter version: `git log -S '<old-term>'` should bottom out at sealed-history commit messages and unavoidable external interfaces. Every other hit is either a missed rename or an active re-infection source.

## Related Notes

- [WIP Files](/git-workflow/wip-files.md)
- [Branch Naming](/git-workflow/branch-naming.md)
- [No Hidden Side Effects](/principles/no-hidden-side-effects.md)
