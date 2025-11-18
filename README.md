# Engineering Standards

This repository contains the engineering standards, design patterns, and development practices for all projects across the organization. These standards ensure consistency, maintainability, and quality across our codebase.

## Overview

This repository serves as the single source of truth for:
- **Code Style & Naming Conventions**: How we name variables, organize files, and structure code
- **Testing Practices**: How we write tests, achieve coverage, and ensure quality
- **Architecture Patterns**: How we design systems, structure components, and manage data flow
- **Development Workflows**: How we work with Git, handle background tasks, and deploy code

## Standards Documentation

### 📐 [Code Style Guide](standards/code-style.md)
- Variable and function naming conventions
- File naming and organization
- Code formatting and structure
- Import/export patterns
- Comments and documentation

### 🧪 [Testing Practices](standards/testing-practices.md)
- Test file structure and naming
- Unit, integration, and E2E testing patterns
- Coverage requirements and strategies
- Mocking and stubbing conventions
- Test data management

### 🏗️ [Architecture Patterns](standards/architecture-patterns.md)
- Component and module structure
- State management patterns
- API integration patterns
- Database access patterns
- Error handling strategies
- Performance optimization guidelines

### 🔄 [Development Workflows](standards/development-workflows.md)
- Git branching and commit strategies
- Pull request guidelines
- Code review checklist
- Background task implementation (jobs, cron, queues)
- Deployment procedures
- Environment management

## Using These Standards

### For Developers

1. Read the relevant standards before starting work on a new feature or project
2. Reference these standards during code reviews
3. Propose changes via pull requests when standards need updating

### For Claude Code

Each project should reference these standards in its `CLAUDE.md` file. See [templates/CLAUDE.md.template](templates/CLAUDE.md.template) for an example.

Claude Code will automatically fetch and apply these standards when working on your codebase.

### For New Projects

1. Copy `templates/CLAUDE.md.template` to your project root as `CLAUDE.md`
2. Customize project-specific details
3. Keep the references to these central standards

## Contributing

Standards should evolve based on:
- Lessons learned from production issues
- New technology adoption
- Team feedback and pain points
- Industry best practices

To propose a change:
1. Create a branch describing the change (e.g., `update-testing-coverage-requirements`)
2. Update the relevant standard document(s)
3. Provide rationale and examples in your PR description
4. Get approval from at least 2 senior engineers

## Version History

Track major changes to standards in [CHANGELOG.md](CHANGELOG.md).

## Questions?

If standards are unclear or don't cover your use case:
1. Check the `examples/` directory for reference implementations
2. Ask in the engineering Slack channel
3. Propose a clarification or addition to the standards

---

**Last Updated**: 2025-11-18
**Maintained By**: Engineering Team
