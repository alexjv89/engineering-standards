# Hub-and-Spoke Documentation Placement

Documentation lives where you'll reach for it.

## Principle

In a hub-and-spoke architecture with multiple repositories, place documentation based on the question: **"Where will I reach for this doc?"**

| If it helps me... | Place it in... |
|---|---|
| Build/maintain the hub (orchestrator) | Hub repo |
| Build processes/automations (domain knowledge) | Standards/IP repo |
| Understand customer-specific context | Spoke (worker) repo |

## Why

- **Reduces context switching** - Docs are where you're already working
- **Clarifies ownership** - Each repo owns its relevant documentation
- **Prevents duplication** - Single source of truth per concern
- **Matches mental model** - When stuck, you check the relevant repo

## Examples

**Hub (Orchestrator) Repo:**
- API contracts for workers
- Step execution lifecycle
- Queue system architecture
- Multi-tenant patterns

**Standards/IP Repo:**
- Accounting rules and edge cases
- Process templates
- Automation patterns
- Domain expertise

**Spoke (Worker) Repo:**
- Customer-specific configurations
- Custom step implementations
- Integration details for that customer
- Deployment instructions for that environment

## Common Mistakes

**Putting everything in the hub:**
Hub becomes bloated; domain knowledge gets lost when building workers.

**Duplicating across repos:**
Creates drift; updates in one place don't propagate.

**Organizing by doc type instead of usage:**
All "architecture docs" in one place ignores where you actually need them.

## Trade-offs

- **Discoverability**: New team members may not know which repo to check
  - Mitigate with a brief "where to find docs" note in each repo's README
- **Cross-cutting concerns**: Some docs span multiple repos
  - Pick the primary audience; link from other repos

## Related Notes
- [Single Idea Per Note](/principles/documentation/single-idea-per-note.md)
- [Module Organization](/architecture/modules/module-organization.md)
