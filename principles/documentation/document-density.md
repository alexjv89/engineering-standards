# Document Density

Be specific about project patterns, trust LLM for standard operations.

## Principle

Document WHAT to do and WHERE to look. Trust LLM knows HOW to use its tools.

**Include**:
- WHAT - Specific actions
- WHERE - File paths, commands, patterns
- WHY - When it adds context
- HOW - When non-obvious or project-specific

**Exclude**:
- HOW for standard LLM tools (Read, Glob, Grep)
- Obvious explanations ("This allows you to...")

## The Balance

**Too Verbose**: "To find all instances of getCwd, you should use the Grep tool to search through the codebase for the pattern 'getCwd'. This will help you locate all occurrences..."

**Too Terse**: "Rename getCwd to getCurrentWorkingDirectory"

**Right Balance**: "Rename getCwd to getCurrentWorkingDirectory across the codebase (15 instances in 8 files)."

## Include HOW (Non-Obvious)

```markdown
✅ Run typecheck: `npm run typecheck` (uses JSDoc, not `tsc`)
✅ Queue job: `addJob('parseFile', { statement_id })`
✅ Search pattern: `\[\[A-Z]+` (finds double-bracket bug)
```

## Exclude HOW (Obvious)

```markdown
❌ "Read the file using the Read tool"
❌ "Search for the pattern using Grep"
❌ "Use Glob to find matching files"
```

## Example: Dense Documentation

```markdown
Search for `\[\[A-Z]+` in src/parsers/
Files: hdfc_cc__pdf.js, icici_cc__pdf.js
Replace `[[` with `[`
```

Everything needed: what, where, why implicit. No tool instructions.

## Related Notes

- [Brevity](/principles/documentation/brevity.md) - Remove filler, keep structure
- [LLM Benefits](/principles/documentation/llm-benefits.md) - Why this helps LLMs
- [Actual Patterns Not Aspirational](/principles/documentation/actual-patterns-not-aspirational.md) - Document what exists
