# Actual Patterns Not Aspirational

Document patterns that are actually implemented and in use, not aspirational or planned patterns.

## The Problem

Documentation that describes non-existent infrastructure creates:
- **Confusion** - Readers can't tell what's real vs. planned
- **Maintenance burden** - Keeping aspirational docs updated
- **Wasted effort** - LLMs load irrelevant context
- **False expectations** - Teams think patterns exist when they don't

## The Principle

**Only document what exists right now.**

If a pattern, tool, or infrastructure isn't implemented:
- Don't write the documentation
- Don't create the files
- Don't reserve space for "future work"

## Examples

### Bad: Aspirational CI/CD Documentation

```
storybook/testing/
├── ci-cd-github-actions.md      ❌ No GitHub Actions workflow exists
├── ci-cd-caching.md             ❌ No CI caching setup
├── ci-cd-artifacts.md           ❌ No artifact uploads
├── ci-cd-optimization.md        ❌ No CI to optimize
└── ci-cd-troubleshooting.md     ❌ No CI to troubleshoot
```

**Problem**: 40% of testing docs describe non-existent CI infrastructure.

**Result**: Deleted all 5 files. When CI is actually implemented, write the docs then.

### Good: Actual Pattern Documentation

```
storybook/testing/
├── coverage-script.md           ✅ scripts/storybook-coverage.js exists
├── coverage-reports.md          ✅ dev_docs/storybook/coverage-report.md exists
├── test-runner-config.md        ✅ .storybook/test-runner.js exists
└── test-runner-hooks.md         ✅ preVisit/postVisit hooks implemented
```

**Verified**: Each file documents code that exists in the consuming project.

## How to Verify

Before creating documentation, verify the pattern exists:

### 1. Check File Exists

```bash
# Document says scripts/coverage.js exists?
ls scripts/coverage.js

# Document says .storybook/test-runner.js has hooks?
grep "preVisit" .storybook/test-runner.js
```

### 2. Check Pattern is Used

```bash
# Document says "always use const meta"?
grep -r "const meta = {" src/**/*.stories.js | wc -l

# If 0 results = pattern isn't used = delete the doc
```

### 3. Check Implementation Matches Documentation

```bash
# Doc says coverage script generates 3 reports
node scripts/storybook-coverage.js
ls dev_docs/storybook/coverage-report.md  # ✅
ls dev_docs/storybook/coverage-badge.svg  # ✅
# Console output                            # ✅
```

All 3 exist = documentation is accurate.

## When to Create Documentation

**Write documentation AFTER implementation, not before:**

```
1. ✅ Implement the pattern/tool/infrastructure
2. ✅ Use it in real code for a few weeks
3. ✅ Verify it works and is stable
4. ✅ Write documentation based on actual usage
```

**Don't write documentation for:**
- Planned features not yet started
- Prototypes or experiments
- "Nice to have" patterns you might adopt
- Tools you're "thinking about using"

## Mixed Implementations

If pattern is partially implemented, be explicit:

### Bad: Implies Full Coverage

```markdown
# Meta Object Pattern

Always use `const meta` for all story files.
```

### Good: Acknowledges Reality

```markdown
# Meta Object Pattern (50% Adoption)

Preferred pattern for new stories. Legacy stories use inline exports.

Current usage:
- 50% use `const meta = { ... }`
- 50% use `export default { ... }`

When creating new stories, use `const meta`.
```

## Red Flags

Documentation that indicates aspirational patterns:

- **Future tense**: "will use", "should implement", "plan to add"
- **Conditionals**: "if you set up CI", "when configured", "after enabling"
- **Zero examples**: No file paths, no code snippets from real codebase
- **"Recommended" without adoption**: Recommends pattern but codebase doesn't use it

## Benefits of Actual-Only Documentation

1. **LLM efficiency** - Only loads relevant, real patterns
2. **No confusion** - Clear what's actually used
3. **Lower maintenance** - Only update when code changes
4. **Honest representation** - Documentation matches reality
5. **Cost reduction** - Fewer tokens, faster context loading

## Related Notes

- [Single Idea Per Note](/principles/documentation/single-idea-per-note.md) - One concept per file
- [Meaningful Titles](/principles/documentation/meaningful-titles.md) - Clear, descriptive filenames
- [Discovery Patterns](/principles/documentation/discovery-patterns.md) - How LLMs find notes
- [LLM Benefits](/principles/documentation/llm-benefits.md) - Why this structure helps LLMs
