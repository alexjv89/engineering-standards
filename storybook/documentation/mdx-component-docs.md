# MDX Component Documentation

Component-level documentation using Storybook MDX files for comprehensive, custom-written documentation.

## What This Is

**MDX** (Markdown + JSX) files allow you to write detailed, custom component documentation with:
- Rich markdown formatting
- Embedded live component examples
- Custom layout and structure
- Full control over documentation content

**Use MDX for**: Component-level documentation
**Use parameters.description for**: Story-level context (see [Custom Description Banner](/storybook/documentation/custom-description-banner.md))

## When to Create MDX Docs

Create `.mdx` files when a component needs:
- **Detailed explanation** of behavior and use cases
- **Multiple usage examples** beyond individual stories
- **API documentation** with parameter descriptions
- **Guidelines** for when to use/not use the component
- **Visual examples** with explanations
- **Migration guides** or upgrade notes

**Example components that benefit from MDX**:
- Complex form components (DatePicker, MultiSelect, FileDropzone)
- Layout components (Grid, Container, PageHeader)
- Data display components (Table, DataGrid)
- Reusable utility components used across the app

## File Naming and Location

MDX files live alongside the component:

```
src/components/
  ├── DatePicker.jsx
  ├── DatePicker.stories.js
  └── DatePicker.mdx          # Component documentation
```

**Naming convention**: `ComponentName.mdx`

## Basic MDX Structure

```mdx
import { Meta, Canvas, Story } from '@storybook/blocks';
import * as DatePickerStories from './DatePicker.stories';

<Meta of={DatePickerStories} />

# DatePicker

A reusable date picker component built on Joy UI with calendar popup and keyboard navigation.

## When to Use

Use DatePicker when you need:
- Single date selection from a calendar
- Date input with validation
- Keyboard-accessible date selection

**Don't use** for date ranges (use DateRangePicker instead).

## Usage

### Basic Usage

Basic usage:

<Canvas of={DatePickerStories.BeforeSelectingDate} />

### With Default Value

Pre-select a date by passing `default_value`:

<Canvas of={DatePickerStories.AfterSelectingDate} />

### With Validation

Restrict selectable dates with `min_date` and `max_date`:

<Canvas of={DatePickerStories.WithDateRestrictions} />

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `default_value` | `string` | `''` | Pre-selected date in YYYY-MM-DD format |
| `on_change` | `function` | required | Callback when date changes, receives date string |
| `placeholder` | `string` | `'Select a date'` | Placeholder text when no date selected |
| `min_date` | `string` | - | Earliest selectable date (YYYY-MM-DD) |
| `max_date` | `string` | - | Latest selectable date (YYYY-MM-DD) |

## Accessibility

- Keyboard navigation: Arrow keys navigate calendar
- Escape key closes calendar
- Screen reader announcements for date selection
- ARIA labels for calendar elements

## Related Components

- `DateRangePicker` - For selecting date ranges
- `TimePicker` - For time selection
```

## MDX vs Autodocs

**We do NOT use Storybook's autodocs feature**. Instead:

| Approach | Level | Format | Control | Used? |
|----------|-------|--------|---------|-------|
| **MDX** | Component-level | Custom-written docs | Full control | ✅ **Yes** |
| **Autodocs** | Component-level | Auto-generated from stories | Limited control | ❌ **No** |
| **parameters.description** | Story-level | Custom banner in canvas | Full control | ✅ **Yes** |

MDX gives us complete control over:
- Documentation structure and flow
- Which examples to show and in what order
- Additional context, guidelines, and best practices
- Visual design of the documentation

## Key MDX Features

### 1. Embedding Stories

Reference stories from your `.stories.js` file:

```mdx
import * as ComponentStories from './Component.stories';

<Canvas of={ComponentStories.AfterUserAction} />
```

### 2. Live Code Examples

Show live, editable code examples:

```mdx
<Canvas>
  <DatePicker
    default_value="2024-01-15"
    on_change={(date) => console.log(date)}
  />
</Canvas>
```

### 3. Markdown Formatting

Use full markdown syntax:

```mdx
## Heading

**Bold text**, *italic text*, `code`.

- Bullet lists
- With multiple items

1. Numbered lists
2. Also supported

> Blockquotes for callouts

| Tables | Are |
|--------|-----|
| Also   | Supported |
```

### 4. Custom JSX Components

Import and use React components:

```mdx
import { Alert } from '@mui/joy';

<Alert color="warning">
  ⚠️ This component is deprecated. Use NewComponent instead.
</Alert>
```

## MDX Template

Create new component documentation using this template:

```mdx
import { Meta, Canvas } from '@storybook/blocks';
import * as ComponentStories from './Component.stories';

<Meta of={ComponentStories} />

# ComponentName

[One-sentence description of what this component does]

## When to Use

Use ComponentName when you need:
- [Use case 1]
- [Use case 2]
- [Use case 3]

**Don't use** for [alternative scenarios].

## Usage

### Basic Usage

[Brief explanation]

<Canvas of={ComponentStories.StoryName} />

### [Additional Usage Pattern]

[Explanation of variant or advanced usage]

<Canvas of={ComponentStories.AnotherStory} />

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `prop_name` | `string` | required | What this prop does |
| `optional_prop` | `boolean` | `false` | What this optional prop does |

## Guidelines

### Do's
- ✅ [Best practice 1]
- ✅ [Best practice 2]

### Don'ts
- ❌ [Anti-pattern 1]
- ❌ [Anti-pattern 2]

## Accessibility

- [Accessibility feature 1]
- [Accessibility feature 2]
- [Keyboard navigation details]

## Related Components

- `RelatedComponent1` - [When to use this instead]
- `RelatedComponent2` - [How this complements it]
```

## Common Patterns

### Props Table

Document all component props:

```mdx
## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `file_name` | `string` | required | Name of the uploaded file |
| `on_upload` | `function` | required | Callback when file is uploaded |
| `accepted_formats` | `string[]` | `['pdf', 'csv']` | Allowed file extensions |
| `max_size_mb` | `number` | `10` | Maximum file size in megabytes |
```

### Usage Guidelines

Explain when to use/not use:

```mdx
## When to Use

Use TransactionsTable when:
- Displaying financial transaction data
- Need sorting and filtering capabilities
- Working with large datasets (virtualized)

**Don't use** for:
- Small datasets (< 10 rows) - use SimpleList instead
- Non-tabular data - use Cards or List components
```

### Code Examples with Explanations

Show examples with context:

```mdx
### Handling Large Datasets

For datasets with 1000+ rows, the table automatically virtualizes:

<Canvas of={TableStories.WithLargeDataset} />

**Note**: Virtual scrolling is enabled automatically when `data.length > 100`.
```

### Migration Guides

Document breaking changes:

```mdx
## Migration from v1 to v2

The `onSelect` prop has been renamed to `on_change`:

```javascript
// Before (v1)
<DatePicker onSelect={(date) => ...} />

// After (v2)
<DatePicker on_change={(date) => ...} />
\```
```

## Storybook Configuration

Ensure `.storybook/main.js` includes MDX support:

```javascript
module.exports = {
  stories: [
    '../src/**/*.mdx',
    '../src/**/*.stories.@(js|jsx)',
  ],
  addons: [
    '@storybook/addon-essentials',
  ],
};
```

## Documentation Hierarchy

Our documentation approach uses three levels:

1. **File-level** (JSDoc in .stories.js) - Explains user journey pattern
2. **Story-level** (`parameters.description`) - Context for each story in canvas
3. **Component-level** (`.mdx`) - Comprehensive component documentation

```
Component.mdx                    ← Component-level (this note)
  └─ Component.stories.js        ← File-level header
       ├─ Story1                 ← Story-level description
       ├─ Story2                 ← Story-level description
       └─ Story3                 ← Story-level description
```

## Benefits of MDX

- **Custom control** - Write exactly the docs you want
- **Rich formatting** - Full markdown + JSX capabilities
- **Live examples** - Embed interactive components
- **Maintainable** - Version-controlled with code
- **Searchable** - Storybook search works with MDX content
- **No auto-generation** - No surprises from autodocs

## When to Skip MDX

Skip creating `.mdx` files for:
- **Simple UI primitives** (Button, Badge, Icon) - stories are self-documenting
- **Internal components** not used by other developers
- **Deprecated components** being phased out
- **Prototype components** not yet stable

Use `parameters.description` in stories as sufficient documentation for these.

## Related Notes
- [Documentation Patterns](/storybook/documentation/documentation.md) - Multi-layer docs strategy
- [Custom Description Banner](/storybook/documentation/custom-description-banner.md) - Story-level canvas descriptions
- [File Header Pattern](/storybook/documentation/file-header-pattern.md) - JSDoc file headers
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - Story philosophy
