# Changelog

A `CHANGELOG.md` records **what** changed, release by release, for the people who consume the thing. It is the curated, human-readable layer above git: git keeps every commit, the changelog surfaces the ones that matter. A [decision record](/git-workflow/decision-records.md) records *why* a ruling was made; the changelog records *what* shipped.

## Format: Keep a Changelog

Use the [Keep a Changelog](https://keepachangelog.com/en/1.0.0/) shape in a top-level `CHANGELOG.md`, newest first, with an `[Unreleased]` section that accumulates entries between releases.

```markdown
# Changelog

All notable changes to <project> will be documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Added
- **<Title>**: `path/or/area` — what changed and why it matters, in a sentence or two.

### Changed
### Fixed
### Removed

## [1.7.0] - 2025-01-23

### Added
- ...
```

- **Sections**: `Added`, `Changed`, `Fixed`, `Removed` (add `Deprecated` / `Security` when they apply). Drop empty sections at release time.
- **Versions**: [semver](https://semver.org/), each with an ISO date. Cut a release by renaming `[Unreleased]` to the version and opening a fresh `[Unreleased]`.
- **Entries** describe user-visible change, not internal churn. Lead with *what* changed; a clause of *why* is welcome, but the full rationale belongs in a [decision record](/git-workflow/decision-records.md).

## Current vocabulary, even in old entries

A changelog is history, but its prose is read in the present. Use current terms and let the **date** be the only historical marker — a frozen entry that uses a retired name as live vocabulary [re-infects](/principles/propagation-risk.md) every reader who greps it. `Renamed foo → bar` is fine (both quoted as identifiers); prose that keeps calling the thing `foo` afterward is not.

## Boundaries

| Artifact | Answers | Granularity |
|---|---|---|
| `CHANGELOG.md` | *what* changed, user-visible | per release |
| git log | *how* the lines changed | per commit |
| [Decision record](/git-workflow/decision-records.md) | *why* we chose this | per decision |

The changelog is a curated subset of git history written for consumers — not a commit dump, and not the place for reasoning. And it is where a note's *own* revision history goes, so the note itself can stay in the present tense — see [Changelog, Not Inline Revision History](/principles/documentation/structure/changelog-not-inline-history.md).

## Related Notes

- [Decision Records](/git-workflow/decision-records.md) — the *why* sibling
- [Changelog, Not Inline Revision History](/principles/documentation/structure/changelog-not-inline-history.md) — send a note's revision history here, not into the note
- [Commit Message Format](/git-workflow/commit-format.md) — the per-commit layer beneath the changelog
- [Propagation Risk](/principles/propagation-risk.md) — why frozen entries use current vocabulary
