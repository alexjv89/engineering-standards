# Issue Files

An issue records a problem someone noticed. A WIP plans the work that solves one. They're different documents with different homes, and an issue doesn't automatically become a WIP.

| | Issue | WIP |
|---|---|---|
| What it is | An observation: what we hit, the evidence, why it matters, the workaround, a proposal | A plan: phases and steps to build the fix |
| Lives in | The repo where the problem was **noticed** | The repo where the fix **lands** |
| Created when | Someone hits a problem | Someone decides to deal with one or more issues (or plans any other work) |
| Ends | Fixed: `RESOLVED`, then deleted. Lived with: stays, with the reason | `COMPLETE`, then deleted |

Example: importing a client's Tally books in `ops/srga` hits the ledger's missing balances API. The issue is written in `ops/srga/docs/issues/`, because that's where the work stopped. If the team decides to build the endpoint, the WIP goes in `apps/ledger.finopsbricks.com/docs/wip/`, because that's the repo that changes.

## Directory Structure

```
docs/
└── issues/
    ├── README.md                          # index: every issue, its priority, its workaround
    ├── ledger-no-bulk-import-endpoint.md  # <where-the-fix-lands>-<problem>
    └── citara-stray-cost-centre-allocations.md
```

Name an issue after where the fix would land and what's wrong: `ledger-…`, `fob-lgr-…`, or the client or company whose data it is.

## File Structure

```markdown
# Issue: [The problem, stated as a fact]

## Status: OPEN
## Priority: [P0–P4]: [one-line reason]
## Bet: [the bet of the work that hit it, or none]
## App: [where the fix would land: an app, library, CLI, or a company's data]

## Summary

[Two or three sentences: what's wrong.]

## What We Hit

[The evidence: the command, the numbers, the rows, file paths with line numbers.]

## Why It Matters

[Who it hurts and how, today and later.]

## Workaround

[What the work that hit it does about it today, or "None".]

## Proposal

[What would fix it, and who would do it.]
```

## Triage: What Happens to an Issue

Each issue is decided once. It has one of four outcomes:

- **We fix it in code we own:**
  - Write a WIP in the repo where the fix lands, and link the issue from it.
  - Set the issue's status to `IN WIP: <repo>/docs/wip/<file>.md`.
  - Several issues can share one WIP.
- **Someone else fixes it,** for example a client correcting their own data or a vendor fixing their API: the issue's Proposal names who acts and what they do. No WIP.
- **We live with it:**
  - Set the status to `LIVING WITH IT: <reason>`.
  - The issue stays, with the workaround written down, so the next person who hits the problem finds it.
- **It isn't a problem:**
  - Delete the issue.
  - Add a line to the README's "Worked As Designed" section saying why.

An open issue with no decision is normal. It waits for triage, carrying its priority.

## Status Values

| Status | Meaning |
|---|---|
| `OPEN` | Noticed and written down; not decided yet |
| `IN WIP: <path>` | Being fixed; the WIP holds the plan |
| `RESOLVED (YYYY-MM-DD): <what changed>` | Fixed, by a WIP or by someone else; say what shipped and how it was checked. Then delete the file |
| `LIVING WITH IT: <reason>` | We accept the problem; the issue stays open as the record of it |

## Priority and Bet

An issue carries a priority from [Priority Levels](/git-workflow/priority-levels.md). Set it from the urgency and impact for whoever hit the problem. When the issue becomes a WIP, the WIP sets its own priority in its own repo, and the two can differ.

It also carries the bet of the work that hit it: the payoff that work serves, or `none`. Deciding which issues to fix spends time, so the decision follows the bets like any other work. A WIP made from an issue usually serves the same bet, even when the fix lands in another repo.

## Linking Issues and WIPs

- The issue's status links its WIP. Use the full path from the workspace root, since the WIP is usually in another repo.
- The WIP's Problem Statement links every issue it solves.
- When the WIP is complete, set each of its issues to `RESOLVED` and remove it from the README index, then delete the issues along with the WIP. Git keeps their history.

## Not an Issue: A Class of Problem

A note that describes a recurring kind of problem has no status and never resolves. It covers what the pattern looks like, how to spot it and how to remediate it. It's knowledge, not an issue: write it as a knowledge note ([Knowledge Base vs. Activity Report](/principles/documentation/structure/knowledge-vs-reports.md)), and let issues link to it.

## Related Notes

- [WIP Files](/git-workflow/wip-files.md) - the plan an issue turns into
- [Priority Levels](/git-workflow/priority-levels.md) - the P0–P4 line every issue carries
- [Decision Records](/git-workflow/decision-records.md) - where a choice made while fixing an issue is settled
- [Knowledge Base vs. Activity Report](/principles/documentation/structure/knowledge-vs-reports.md) - why a class of problem is a note, not an issue
