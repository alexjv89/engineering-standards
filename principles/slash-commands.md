# Slash Command Writing Principles

Slash commands are orchestration scripts for LLMs — they tell the LLM **what to do**, not how to understand a standard. Keep them lean, process-focused, and free of embedded conventions.

## Core Principle: Orchestrate, Don't Educate

| ✅ Command Should Contain | ❌ Command Should NOT Contain |
|--------------------------|-------------------------------|
| Output file location | Convention rules |
| List of standards files to read | Code examples |
| Numbered process steps | Good/bad pattern examples |
| Report format template | Specific naming conventions |
| Allowed tools | Project-specific content |

**The test**: If you find yourself writing a rule ("use camelCase here", "always add @param"), stop. Create an engineering standards note instead, then reference it by file path.

## Anatomy of a Slash Command

```markdown
---
description: One-sentence description of what this command does
frequency: monthly | on-demand | per-session
output: path/to/output-file.md
allowedTools: [Bash, Read, Glob, Grep]
---

# Command Title

One sentence purpose.

## Output

**Report file:** `path/to/output.md` (overwrites previous)

## Standards to Apply

Read these files before proceeding:

| Standard | File |
|----------|------|
| [Name] | `~/ec2code/alex/engineering-standards/[path].md` |

## Process

1. **Read all standards** listed above
2. **Find target files** using Glob/Grep
3. **Apply or check** against standards
4. **Generate output** in format below

## Output Format

[Markdown template for the result]

---

Now execute.
```

Not every section is required. Action commands often skip "Output Format". Workflow commands often skip "Standards to Apply". Use only what the command needs.

## Types of Slash Commands

### Review Commands
Audit the codebase, generate a report. Must have: standards reference, output file, report template.
Example: `code:review:jsdoc:coverage`

### Action Commands
Modify files or run operations. Must have: clear process steps, result summary.
Example: `code:dev:stage`, `code:dev:add-types`

### Workflow Commands
Guide a multi-step development process. Must have: pre-flight checks, sequential phases, verification steps.
Example: `code:dev:build-feature`

### Generator Commands
Create new files from templates. Must have: input gathering, file location, confirmation.
Example: `code:generators:create-review-command`

## Length Guidelines

| Type | Target Lines |
|------|-------------|
| Action commands | 20–50 |
| Review commands | 40–80 |
| Workflow commands | 60–120 |

Going over is a signal that education has crept in.

## Anti-Patterns

**Inline conventions** — if you're writing rules, move them to engineering standards:
```
❌  Use @param for each parameter with the exact type, not just {Object}
✅  Read architecture/jsdoc/syntax/jsdoc-basic-annotations.md first
```

**Project-specific content** — parser names, component lists, hardcoded file paths, domain types. These belong in the project's own CLAUDE.md or a project-local slash command:
```
❌  Look for "New parsers for bank statements" as MINOR bump indicators
✅  Categorize changes from commit messages and diff output
```

**No output specification** — every command should state where output goes and in what format.

**Vague commands** — 4 lines of instruction with no structure is not a slash command. Include specific numbered steps or delete.

**Dead-end commands** — if the "command" is just a tone/mode reminder ("be concise, ask questions"), it doesn't need to be a slash command. It's just a prompt.

## Related Notes
- [Document Density](/principles/documentation/document-density.md)
- [Single Idea Per Note](/principles/documentation/single-idea-per-note.md)
- [Brevity](/principles/documentation/brevity.md)
