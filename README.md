# Engineering Standards - Atomic Knowledge Base

An atomic knowledge base of engineering standards designed for efficient LLM consumption. Each note focuses on a single concept, pattern, or practice.

## What is This?

This repository contains **47+ atomic notes** organized into focused topics:
- **Naming** (11 notes) - Variables, functions, components, files, types
- **Testing** (9 notes) - Test patterns, coverage, mocking, React testing
- **Architecture** (20 notes) - Components, state, modules, database patterns
- **Git Workflow** (7 notes) - Branches, commits, PRs, background tasks

### For Small Teams

These are **simple, opinionated standards** designed for **small teams** with specific constraints and preferences:

✅ **Minimized Complexity** - JavaScript with JSDoc instead of TypeScript
✅ **Functional-First** - Functional programming over OOP for business logic
✅ **Modern React** - Hooks, functional components, minimal classes
✅ **Fewer Moving Parts** - Reduced build steps and tooling overhead

**Philosophy documents:**
- [JavaScript with JSDoc](./principles/javascript-with-jsdoc.md) - Why plain JavaScript
- [Functional Programming](./principles/functional-programming.md) - When to use FP vs OOP

## Why Atomic Notes?

Traditional comprehensive documentation is inefficient for LLM tools. This knowledge base uses **atomic notes** - each addressing one specific concept - for:

✅ **Cost Efficiency** - Load only relevant notes, not entire documents
✅ **Clear Context** - Each note is self-contained and focused
✅ **Easy Discovery** - Find exactly what you need via search or index
✅ **Emergent Organization** - Knowledge graph emerges from inter-note links

**Read more**: [principles/atomic-knowledge-base.md](./principles/atomic-knowledge-base.md)

## Repository Structure

```
/
├── principles/         # Knowledge base principles
│   └── atomic-knowledge-base.md
├── naming/            # Naming conventions (11 notes)
│   ├── index.md
│   ├── variables-snake-case.md
│   ├── components-pascalcase.md
│   └── ... (8 more)
├── testing/           # Testing practices (9 notes)
│   ├── index.md
│   ├── aaa-pattern.md
│   ├── coverage-requirements.md
│   └── ... (6 more)
├── architecture/      # Architecture patterns (20 notes)
│   ├── index.md
│   ├── database/      # Database patterns (11 notes)
│   ├── components/    # Component patterns
│   ├── state/         # State management
│   ├── modules/       # Module organization
│   └── functional/    # Functional programming
├── git-workflow/      # Git & development workflow (7 notes)
│   ├── index.md
│   ├── branch-naming.md
│   ├── commit-format.md
│   └── ... (4 more)
├── examples/          # Complex code examples (future)
└── templates/         # Project templates
    └── CLAUDE.md.template
```

## Quick Start

### For Humans

Browse by topic:
1. Start with a topic `index.md` file (e.g., `naming/index.md`)
2. Click through to specific notes of interest
3. Follow "Related Notes" links to discover connections

### For LLMs

Search and load atomically:
```bash
# Find all naming notes
ls naming/*.md

# Search for specific concept
grep -r "snake_case" --include="*.md"

# Load specific note
cat naming/variables-snake-case.md
```

**Best practice**: Start with topic index, identify relevant notes, load only what's needed.

## Common Use Cases

### Naming a React Component
1. Read `naming/components-pascalcase.md`
2. Follow to `naming/files-components.md`
3. If using hooks: `naming/hooks-use-prefix.md`

### Writing Tests
1. Start at `testing/index.md`
2. Read `testing/aaa-pattern.md` for structure
3. Read `testing/coverage-requirements.md` for targets
4. Read `testing/testing-react-components.md` for React

### Setting Up Git Workflow
1. Start at `git-workflow/index.md`
2. Read `git-workflow/branch-naming.md`
3. Read `git-workflow/commit-format.md`
4. Read `git-workflow/pull-request-guidelines.md`

### Designing Component Architecture
1. Start at `architecture/index.md`
2. Read `architecture/container-presentational-pattern.md`
3. Read `architecture/component-composition.md`
4. Follow links to state management patterns

## For LLM Agents & Skills

This knowledge base is optimized for LLM consumption:

- **Load selectively**: Only fetch notes relevant to current task
- **Follow links**: Navigate through related concepts as needed
- **Index first**: Check `index.md` files for overview
- **Stay focused**: Each note addresses one concept

See [CLAUDE.md](./CLAUDE.md) for detailed usage guidance.

## For Other Projects

**Do NOT** copy these notes into your project. Instead:

1. Reference this repository in your project's `CLAUDE.md`
2. Link to specific notes when documenting patterns
3. Treat this as the source of truth for standards

See [templates/CLAUDE.md.template](./templates/CLAUDE.md.template) for how to integrate these standards into your project.

## Contributing

### Adding a New Note

1. Determine the correct topic directory
2. Create focused note (20-50 lines typical):
   - Clear title describing single concept
   - Code example(s)
   - Links to 2-5 related notes
3. Update directory `index.md`
4. Add cross-references from related notes

### Updating Notes

1. Preserve single-concept focus
2. Update examples for clarity
3. Maintain cross-references
4. Update CHANGELOG.md for significant changes

### When a Note Gets Too Large

If a note exceeds ~60 lines:
1. Identify sub-concepts
2. Split into multiple atomic notes
3. Update original note with links
4. Update index

## Atomic Note Structure

Each note follows this pattern:

```markdown
# [Clear Descriptive Title]

[One-paragraph summary]

## [Pattern/Example/Usage Section]

[Code example or explanation]

## Related Notes
- [Link to related concept 1](./related-note.md)
- [Link to related concept 2](../other-dir/note.md)
```

**Key properties**:
- Single idea per note
- Concise (20-50 lines typical)
- Practical code examples
- 2-5 related note links

## Version History

See [CHANGELOG.md](./CHANGELOG.md) for version history and major changes.

## Questions?

- **For LLM usage**: See [CLAUDE.md](./CLAUDE.md)
- **For atomic principles**: See [principles/atomic-knowledge-base.md](./principles/atomic-knowledge-base.md)
- **For specific standards**: Browse topic directories

---

**Maintained for**: LLM-powered development tools, AI coding assistants, engineering teams
**Last Updated**: 2025-11-18
**Note Count**: 47+ atomic notes across 4 topics
