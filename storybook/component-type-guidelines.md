# Component Type Guidelines

Play function usage and story count recommendations by component type.

## Guidelines by Type

| Component Type | Play Functions | Story Focus | Story Count |
|---------------|----------------|-------------|-------------|
| Forms/Inputs | ✅ Yes | Interactions, validation | 3-6 |
| Dialogs/Modals | ✅ Yes | User actions, workflows | 3-6 |
| Tables/Lists | ❌ No | Data states, edge cases | 5-10 |
| Cards/Display | ❌ No | Visual states | 1-3 |
| Buttons/Atoms | Optional | Visual variants | 1-2 |

## Pattern

**Interactive components** (forms, dialogs) → Use play functions to automate interactions
**Display components** (tables, cards) → Focus on data states without play functions
**Atomic components** (buttons) → Minimal stories, play functions optional

## Related Notes
- [Play Functions Basics](/storybook/play-functions/basics.md)
- [User Journey Pattern](/storybook/storybook-user-journey-pattern.md)
- [Story Naming](/storybook/organization/story-naming.md)
