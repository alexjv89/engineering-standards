# WIP Files Pattern

Work-in-progress (WIP) files track ongoing implementation tasks, refactoring projects, and feature development. They serve as living documents that guide work and maintain context across sessions.

## Directory Structure

```
docs/
└── wip/
    ├── feature-name.md           # Main WIP files
    ├── refactoring-task.md
    └── subdomain/                 # Optional subdirectories for groupings
        └── specific-task.md
```

**Location**: Always `docs/wip/` - tracked in git, visible to team.

## File Naming

Use kebab-case with descriptive names:

```
statement-period-filter.md        # Feature implementation
parser-conflict-analysis.md       # Investigation/analysis
utility-refactor-checklist.md     # Refactoring task
api-endpoint-planning.md          # Planning document
```

## File Structure

```markdown
# [Clear Descriptive Title]

## Status: [STATE]

[One-paragraph summary of what this work accomplishes]

---

## Problem Statement

[Why this work is needed - current situation and desired outcome]

## Proposed Solution

[High-level approach with key decisions]

## Implementation Phases

### Phase 1: [Name] ✅
- [x] Sub-task completed
- [x] Another completed item

### Phase 2: [Name] 🔄
- [x] Sub-task done
- [ ] Sub-task in progress
- [ ] Sub-task pending

### Phase 3: [Name] ❌
- [ ] Not started yet

## Related Files

- `src/path/to/file.js` - Brief description
- `src/another/file.js` - Brief description
```

## Status Values

| Status | Meaning | Example |
|--------|---------|---------|
| `IN PROGRESS (~X%)` | Active work with estimate | `Status: IN PROGRESS (~65%)` |
| `COMPLETE` | All work done | `Status: COMPLETE` |
| `NOT STARTED` | Planned, no work yet | `Status: NOT STARTED` |
| `BLOCKED` | Waiting on dependency | `Status: BLOCKED - awaiting API` |
| `EVALUATION` | Analysis/research phase | `Status: EVALUATION` |

## Phase Markers

| Marker | Meaning |
|--------|---------|
| ✅ | Phase complete |
| 🔄 | Phase in progress (current) |
| ❌ | Phase not started |
| ⚠️ | Phase blocked/needs attention |

## Lifecycle

### Creating a WIP File

Create when starting non-trivial work:

1. Create `docs/wip/task-name.md`
2. Add status, problem statement, proposed solution
3. Break into phases with checkboxes
4. Commit: `docs: add WIP for task-name`

### Updating During Work

Update as you progress:

1. Check off completed items `[x]`
2. Update status percentage
3. Add notes about decisions or blockers
4. Commit changes with regular work

### Completing and Clearing

When work is done:

1. Mark status as `COMPLETE`
2. Verify all phases show ✅
3. Delete the file: `git rm docs/wip/task-name.md`
4. Commit: `docs: remove completed WIP task-name`

## WIP Review Process

Periodically review WIP files to identify:

- **Complete**: Work done, file can be deleted
- **Partial**: Some work done, needs continuation
- **Stale**: No progress for extended period - continue or abandon
- **Unclear**: Cannot determine status - needs manual review

## Integration with Claude Commands

WIP files can be paired with slash commands for guided execution:

```
docs/wip/parser-migration.md          # Detailed checklist
.claude/commands/wip/migrate-parser.md # Execution command
```

The command references the WIP file and guides step-by-step execution.

## Best Practices

**DO:**
- Keep status current - update as you work
- Use specific, verifiable completion criteria
- Include file paths for related code
- Delete when complete - don't let WIP files accumulate

**DON'T:**
- Create WIP files for trivial tasks
- Leave outdated WIP files in the repo
- Embed full documentation (link to it instead)
- Track multiple unrelated tasks in one file

## Related Notes

- [Branch Naming](/git-workflow/branch-naming.md) - Branch names may reference WIP tasks
- [Commit Format](/git-workflow/commit-format.md) - Commit messages for WIP updates
- [Pull Request Guidelines](/git-workflow/pull-request-guidelines.md) - PRs often close WIP work
