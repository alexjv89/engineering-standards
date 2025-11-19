# Custom Story Description Banner

Custom Storybook pattern that displays story descriptions directly in the canvas using `parameters.description`.

## What This Is

This is a **custom implementation** (not standard Storybook) that displays story context directly in the visual canvas as a banner above the component.

**Implementation**: Custom decorator in `.storybook/preview.js`

```javascript
decorators: [
  (Story, context) => {
    const description = context.parameters?.description;

    return (
      <div>
        {description && (
          <div className="px-4 py-2 mb-2 bg-sky-50 border-b-1 border-sky-600 text-sm leading-relaxed text-slate-700">
            <span className='font-semibold'>Description: </span>{description}
          </div>
        )}
        <Story />
      </div>
    );
  },
],
```

## Why This Exists

Standard Storybook has `parameters.docs.description.story` which appears in the **Docs tab**, but this is NOT visible when viewing the story canvas directly.

This custom banner solves that by showing story context **in the canvas itself**, making it immediately visible when reviewing stories.

## Usage in Stories

Add `parameters.description` to any story:

**Standard order**: `parameters` before `args`

```javascript
export const AfterCreatingOrg = {
  parameters: {
    description: 'Happy path: User views their organization card without custom description. Shows organization name and creation date. This is the most common scenario. Card is clickable and navigates to org dashboard.',
  },
  args: {
    org: {
      id: 'org_123',
      slug: 'acme-corporation',
      name: 'Acme Corporation',
      description: null,
      created_at: '2024-01-15T10:30:00Z',
    },
  },
};
```

**Result**: A blue banner appears above the component in the canvas showing the description.

**Note**: We do NOT use Storybook's autodocs feature. The `parameters.description` custom banner is our primary documentation method for stories.

## Description Content Pattern

Descriptions should explain the **user journey context** for this story:

**Format**: `[Journey Phase]: [What user sees/experiences]. [Additional context]. [Testing notes]`

### Good Examples

```javascript
parameters: {
  description: 'Happy path: User views their organization card without custom description. Shows organization name and creation date. This is the most common scenario. Card is clickable and navigates to org dashboard.',
}
```

```javascript
parameters: {
  description: 'Edge case: Organization with very long official name. Tests that card layout handles long text with truncation (ellipsis). Common with legal company names that include suffixes like "Private Limited".',
}
```

```javascript
parameters: {
  description: 'When creating org, we ask for pan number. This is stored in description field. When this field is set, this shown. if not we show created at.',
}
```

**Pattern breakdown**:
1. **Journey phase** - "Happy path", "Edge case", "When [condition]"
2. **Visual outcome** - What the user sees
3. **Context** - Why this matters or when it happens
4. **Testing notes** - What this story tests (optional)

## Difference from Standard Storybook

| Feature | Location | When Visible | Purpose | Used? |
|---------|----------|-------------|---------|-------|
| `parameters.description` | **Story canvas** (custom) | Always visible when viewing story | Immediate context for reviewers | ✅ **Yes** |
| `parameters.docs.description.story` | Docs tab (standard) | Only in Storybook Docs | Documentation generation | ❌ **No** (autodocs not used) |
| `parameters.docs.description.component` | Docs tab (standard) | Only in Storybook Docs | Component-level docs | ❌ **No** (use `.mdx` instead) |

## Documentation Philosophy

**We do NOT use Storybook's autodocs feature**. Instead:

- **Story-level docs**: Use `parameters.description` (custom banner in canvas)
- **Component-level docs**: Use `.mdx` files for detailed component documentation (see [MDX Component Docs](/storybook/documentation/mdx-component-docs.md))

This approach provides:
- Immediate visual context in the canvas
- Custom-written, comprehensive component documentation
- No reliance on auto-generated docs

## Complete Example

```javascript
/**
 * USER JOURNEY STORY: After Creating Org
 *
 * When: User successfully created an organization without adding a custom description
 *
 * User sees: Organization card with name and creation date
 *
 * Tests: Normal card display, date formatting, hover states, navigation link
 */
export const AfterCreatingOrg = {
  parameters: {
    description: 'Happy path: User views their organization card without custom description. Shows organization name and creation date. This is the most common scenario. Card is clickable and navigates to org dashboard.',
  },
  args: {
    org: {
      id: 'org_123',
      slug: 'acme-corporation',
      name: 'Acme Corporation',
      description: null,
      created_at: '2024-01-15T10:30:00Z',
    },
  },
};
```

This story has **two documentation layers**:
1. **JSDoc comment** - For developers reading the code
2. **Custom banner** (`parameters.description`) - For visual reviewers in canvas

## Reducing Duplication

Story context currently appears in both JSDoc comments and `parameters.description`. This creates some duplication but ensures:
- Developers see context when reading code (JSDoc)
- Reviewers see context when viewing stories in canvas (banner)

**Future enhancement**: Auto-extract descriptions from JSDoc to eliminate duplication. See [Auto-Extract Description from JSDoc](/storybook/documentation/auto-extract-description-from-jsdoc.md) for exploration.

## Implementation Details

The custom decorator:
- Lives in `.storybook/preview.js`
- Applies to ALL stories globally
- Reads `context.parameters?.description`
- Renders a styled banner with Tailwind classes:
  - `bg-sky-50` - Light blue background
  - `border-b-1 border-sky-600` - Bottom border
  - `text-sm leading-relaxed text-slate-700` - Typography
  - `font-semibold` on "Description:" label

## Customization Options

If you want to modify the banner appearance, edit the decorator in `.storybook/preview.js`:

```javascript
// Change colors
<div className="px-4 py-2 mb-2 bg-amber-50 border-b-1 border-amber-600">

// Change positioning
<div className="px-8 py-4 mb-4">

// Change typography
<div className="text-base font-normal">
```

## Related Notes
- [Documentation Patterns](/storybook/documentation/documentation.md) - Multi-layer docs approach
- [MDX Component Docs](/storybook/documentation/mdx-component-docs.md) - Component-level MDX documentation
- [Story Naming](/storybook/organization/story-naming.md) - User journey naming
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - Overall pattern philosophy
- [Parameters Structure](/storybook/organization/parameters.md) - Parameters configuration
- [Auto-Extract Description from JSDoc](/storybook/documentation/auto-extract-description-from-jsdoc.md) - Future enhancement exploration
