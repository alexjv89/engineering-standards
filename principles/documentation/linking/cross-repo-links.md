# Cross-Repo Links

Link to another repository by its **GitHub URL**, never a local path.

## Principle

Within one repo, use [repo-root-relative paths](/principles/documentation/linking/cross-linking-notes.md) (`/dir/file.md`). **Across repos, that breaks.** A root-relative or `../` path silently assumes the reader has the *other* repo cloned, at a location your link can reach. Neither is guaranteed — a teammate, a CI job, or an LLM may have only this repo, or have the other one at a completely different path.

A **GitHub URL resolves for everyone**: no local checkout, no shared directory layout.

## Format

```
https://github.com/<owner>/<repo>/blob/main/<path>
```

- Pin to `main` for living docs — you want the current version. Use a commit SHA only when you need a permanent snapshot of a line that will move.
- Add `#L123` to point at a specific line.
- Keep readable link text; the URL is the target, not the label.

## Example

```markdown
<!-- WRONG — assumes the reader has the repo, at your path -->
See the [WIP file standard](/git-workflow/wip-files.md).
See [WIP files](../../alex/engineering-standards/git-workflow/wip-files.md).

<!-- RIGHT — resolves for anyone -->
See the [WIP file standard](https://github.com/alexjv89/engineering-standards/blob/main/git-workflow/wip-files.md).
```

## Same repo? Don't use a GitHub URL

Inside one repo a GitHub URL is worse than a root-relative path — it leaves the repo on every click, pins to one branch, and dies offline. Use `/dir/file.md` for same-repo links; reserve GitHub URLs for crossing a repo boundary. The rule is simply: **same repo → root-relative; different repo → GitHub URL.**

## Related Notes
- [Cross-Linking Notes](/principles/documentation/linking/cross-linking-notes.md) — same-repo linking (root-relative paths)
- [Hub and Spoke Doc Placement](/principles/documentation/structure/hub-spoke-doc-placement.md) — which repo a doc belongs in
- [Meaningful Titles](/principles/documentation/voice/meaningful-titles.md)
