# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is an **Atomic Knowledge Base** for engineering standards - a collection of focused, single-concept notes designed for efficient LLM consumption. Each note addresses one specific pattern, practice, or convention.

## Purpose

This knowledge base serves as:
- **Context Source for LLM Tools**: Provide relevant standards to LLM-powered development tools (agents, skills, slash commands)
- **Cost-Efficient Reference**: Load only the specific notes needed for a task, not entire comprehensive documents
- **Skill/Agent Knowledge**: Individual atomic notes can be loaded as context for specific coding tasks

## Development Philosophy

These standards are designed for **small teams** with specific preferences:

- **JavaScript-First**: Plain JavaScript with JSDoc instead of TypeScript (see `principles/javascript-with-jsdoc.md`)
- **Functional Programming**: Functional patterns over OOP for business logic (see `principles/functional-programming.md`)
- **Minimal Complexity**: Fewer build steps, simpler tooling, reduced moving parts
- **Modern React**: Hooks and functional components; classes only for Error Boundaries

**Key Naming Convention**: Variables use `snake_case`, BUT React hook returns use `camelCase` (React ecosystem convention)

## Repository Structure

```
/
├── principles/         # Core knowledge base principles
├── naming/            # Naming conventions (11 atomic notes)
├── testing/           # Testing practices (9 atomic notes)
├── architecture/      # Architecture patterns (9 atomic notes)
├── git-workflow/      # Git & dev workflow (7 atomic notes)
├── deployment/        # CI/CD and deployment patterns (5 atomic notes)
├── examples/          # Complex code examples (referenced by notes)
├── templates/         # Templates for consuming projects
└── README.md          # Human-facing introduction
```

## Key Principle: Atomic Notes

Each note follows the atomic knowledge base approach:

1. **Single Idea Per Note** - One concept, fully explained
2. **Meaningful Title** - Clear, descriptive filename
3. **Links Over Hierarchy** - Notes connect through links
4. **Emergent Structure** - Organization emerges from connections

**Read**: `principles/documentation/` for full details on atomic knowledge base approach.

## How to Use This Knowledge Base

### For LLM Agents/Skills

When building or maintaining code:

1. **Search by topic**: List files in relevant directory (e.g., `ls naming/`, `ls testing/`)
2. **Load specific notes**: Fetch only the atomic notes needed for the task
3. **Follow links**: Related notes are cross-linked

**Example**: For naming a new React component:
- Read: `naming/components-pascalcase.md`
- Follow to: `naming/files-components.md`
- Follow to: `naming/hooks-use-prefix.md` (if needed)

### For Finding Standards

**By file pattern matching**:
```bash
# Find all notes about naming
ls naming/*.md

# Find testing-related notes
ls testing/*.md
```

**By keyword search**:
```bash
# Find notes mentioning "React"
grep -r "React" --include="*.md"

# Find notes about async
grep -r "async" --include="*.md"
```

## Common Use Cases

### Naming a Variable
1. List files: `ls naming/`
2. Read `naming/variables-snake-case.md`
   - Note: React hook returns use camelCase (exception)
   - Regular variables use snake_case
3. If boolean, read `naming/boolean-prefix.md`
4. If constant, read `naming/constants-screaming-snake.md`

### Writing Tests
1. List files: `ls testing/`
2. Read `testing/aaa-pattern.md` for structure
3. Read `testing/test-naming.md` for naming
4. Read `testing/coverage-requirements.md` for targets

### Designing Components
1. List files: `ls architecture/`
2. Read `architecture/container-presentational-pattern.md`
3. Read `architecture/component-composition.md`
4. Follow links to related patterns

### Setting Up Git Workflow
1. List files: `ls git-workflow/`
2. Read `git-workflow/branch-naming.md`
3. Read `git-workflow/commit-format.md`
4. Read `git-workflow/pull-request-guidelines.md`

## Maintenance Tasks

### Adding a New Atomic Note

1. Determine correct directory (`naming/`, `testing/`, etc.)
2. Create note following atomic pattern:
   - Clear title (one concept)
   - Concise explanation (20-50 lines typical)
   - Code example(s)
   - Links to related notes
3. Add cross-references from related notes

### Updating an Existing Note

1. Read the note and understand its single concept
2. Make changes while preserving focus on that concept
3. Update examples if needed
4. Check and update cross-references
5. If the change is significant, update CHANGELOG.md

### When a Note Gets Too Large

If a note exceeds ~60 lines, consider splitting:

1. Identify sub-concepts within the note
2. Create separate notes for each sub-concept
3. Update original note to link to new notes

**Example**: If `testing/react-components.md` gets large:
- Split into: `testing/react-queries.md`, `testing/react-events.md`, `testing/react-async.md`
- Each focuses on one aspect

## This Is a Documentation Repository

**Important**: This repository contains only markdown files. There is:
- No build process
- No tests to run
- No deployment pipeline
- No code to execute

All work involves editing markdown files. The "testing" is ensuring examples are clear and links work.

## Note Structure Template

When creating new atomic notes, follow this structure:

```markdown
# [Clear Descriptive Title]

[One-paragraph summary of the single concept]

## [Section: Pattern/Example/Usage]

[Code example or explanation]

## [Optional: Additional Context]

[More details if needed, but keep focused]

## Related Notes
- [Link to related concept 1](/directory/related-note.md)
- [Link to related concept 2](/other-dir/note.md)
```

Keep notes:
- **Focused**: One idea only
- **Concise**: Usually 20-50 lines
- **Practical**: Include code examples
- **Connected**: Link to 2-5 related notes

## Cross-Directory Links

Use repo-root relative paths (starts with `/`):

```markdown
# From naming/components-pascalcase.md
See also:
- [Custom Hooks Pattern](/architecture/components/custom-hooks.md)
- [Component Files](/naming/files-components.md)
```

**Benefits**:
- Links remain valid when reorganizing files into different directories
- Works everywhere: GitHub web preview, local editors, IDEs, LLM consumption
- Clear absolute reference from repo root

## For Consuming Projects

Other projects should NOT copy these notes. Instead:

1. Reference this repository in their `CLAUDE.md`
2. Link to specific notes when needed
3. Use this as source of truth for standards

See `templates/CLAUDE.md.template` for how projects should reference this knowledge base.

## Discovery Patterns

**LLMs should**:
1. List files in relevant directory (e.g., `ls naming/` or glob pattern `naming/*.md`)
2. Identify relevant notes from filenames (titles are descriptive)
3. Load only the needed atomic notes
4. Follow cross-reference links as needed

**Avoid**:
- Loading all notes at once
- Reading notes not relevant to current task
- Duplicating note content elsewhere

## Quality Checklist for Notes

When creating or editing notes, ensure:

- [ ] Title clearly describes single concept
- [ ] Content focused on one idea
- [ ] Length appropriate (20-50 lines typical)
- [ ] Includes practical code example
- [ ] Links to 2-5 related notes
- [ ] Cross-referenced from related notes

---

**Remember**: This knowledge base optimizes for LLM consumption. Each atomic note should be independently useful and context-efficient.
