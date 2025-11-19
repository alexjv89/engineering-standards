# Storybook Documentation Strategy

Multi-layer documentation approach for Storybook: file headers, story descriptions, and component documentation.

## The Three Documentation Layers

Our Storybook documentation uses three distinct, complementary layers:

### Layer 1: File Header (JSDoc)
**Purpose**: Orient developers to user journey pattern
**Location**: Top of every `.stories.js` file
**Read**: [File Header Pattern](/storybook/documentation/file-header-pattern.md)

### Layer 2: Story Description (Canvas Banner)
**Purpose**: Explain user context for each story
**Location**: `parameters.description` in each story
**Read**: [Custom Description Banner](/storybook/documentation/custom-description-banner.md)

### Layer 3: Component Documentation (MDX)
**Purpose**: Comprehensive component-level docs
**Location**: `.mdx` file alongside component
**Read**: [MDX Component Docs](/storybook/documentation/mdx-component-docs.md)

## Why Three Layers?

Each layer serves a distinct purpose without overlap:

1. **File header** - Explains the pattern (once per file)
2. **Story description** - Explains the scenario (once per story, visible in canvas)
3. **Component docs** - Explains the component (comprehensive guide)

## Documentation Hierarchy

```
ComponentName.mdx                    ← Layer 3: Component-level docs
  └─ ComponentName.stories.js        ← Layer 1: File header (pattern explanation)
       ├─ Story1                      ← Layer 2: Story description (scenario context)
       ├─ Story2                      ← Layer 2: Story description (scenario context)
       └─ Story3                      ← Layer 2: Story description (scenario context)
```

## When to Use Each Layer

### Always Required
- **File header** - Every story file needs this
- **Story descriptions** - For all non-trivial stories

### Optional (Based on Component Complexity)
- **MDX documentation** - Only for complex components

**Create `.mdx` for**:
- Complex form components (DatePicker, MultiSelect)
- Reusable components used across the app
- Components with multiple use cases

**Skip `.mdx` for**:
- Simple UI primitives (Button, Badge)
- Self-documenting components

## Important: We Don't Use Autodocs

**We do NOT use Storybook's autodocs feature**. Instead:
- Story-level context: Custom `parameters.description` banner in canvas
- Component-level docs: Custom-written `.mdx` files

This gives us full control over documentation content and structure.

## Quick Example

**File header** (Layer 1):
```javascript
/**
 * DatePicker Stories - User Journey Pattern
 *
 * These stories represent real user scenarios/journeys.
 * Each story answers: "When/why does a user see this component?"
 */
```

**Story description** (Layer 2):
```javascript
export const AfterSelectingDate = {
  parameters: {
    description: 'Happy path: User selected a date from calendar. Shows formatted date in input.',
  },
  args: { default_value: '2024-01-15' },
};
```

**Component docs** (Layer 3):
```mdx
# DatePicker

Date selection component with calendar popup.

## When to Use
Use DatePicker when you need single date selection...

## Props
| Prop | Type | Description |
|------|------|-------------|
...
```

## Related Notes
- [File Header Pattern](/storybook/documentation/file-header-pattern.md) - JSDoc headers for story files
- [Custom Description Banner](/storybook/documentation/custom-description-banner.md) - Story-level canvas descriptions
- [MDX Component Docs](/storybook/documentation/mdx-component-docs.md) - Component-level documentation
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - Overall philosophy
- [Story Naming](/storybook/organization/story-naming.md) - User journey naming conventions
