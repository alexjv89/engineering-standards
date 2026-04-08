# CRUD Page Components: List/View/Create/Edit Prefix

CRUD page client components use an action-prefix naming pattern so their role is obvious from the filename alone: `List<Resource>`, `View<Resource>`, `Create<Resource>`, `Edit<Resource>`.

This applies on top of the general [PascalCase rule for component files](/naming/files-components.md) — the prefix names the *action*, the suffix names the *resource*.

## Pattern

| Role | File name | Typical location |
|---|---|---|
| List view | `List<Resource>.jsx` | `<resource>/` (next to `page.jsx`) |
| Detail view | `View<Resource>.jsx` | `<resource>/[id]/` |
| Create form (as a page) | `Create<Resource>.jsx` | `<resource>/create/` |
| Edit form (as a page) | `Edit<Resource>.jsx` | `<resource>/[id]/edit/` |
| Create/Edit form (as a dialog) | `Create<Resource>.jsx` / `Edit<Resource>.jsx` | `<resource>/_components/` |

The resource noun is the singular form for detail/create/edit and the plural form for list:

```
ListStatements.jsx   // plural: the list shows many
ViewStatement.jsx    // singular: the detail shows one
CreateStatement.jsx  // singular: creating one
EditStatement.jsx    // singular: editing one
```

The default-exported function matches the filename:

```jsx
// ListStatements.jsx
export default function ListStatements({ params, org, statements }) {
  // ...
}
```

## Why the prefix matters

Without a prefix, list, detail, create, and edit components all end up named after the same noun and are impossible to distinguish at a glance:

```
rate-cards/
├── RateCards.jsx           // list? detail? root component?
├── [rate_card_id]/
│   └── RateCard.jsx        // unclear
└── create/
    └── RateCard.jsx        // unclear
```

With the prefix, intent is immediate:

```
rate-cards/
├── ListRateCards.jsx       // list page
├── [rate_card_id]/
│   └── ViewRateCard.jsx    // detail page
└── create/
    └── CreateRateCard.jsx  // create page
```

It also makes grep and file navigation trivial: `rg ^ListR` finds every list page, `rg ^Edit` finds every edit form, etc.

## Create/Edit: page vs dialog

Create and edit are **rarely built as separate pages** — usually they appear as a dialog, sheet, or inline form triggered from the list or detail page. In that case the component lives in `<resource>/_components/` instead of `<resource>/create/` or `<resource>/[id]/edit/`:

```
rules/
├── ListRules.jsx
├── _components/
│   └── CreateRule.jsx      // dialog opened from ListRules
└── [r_id]/
    ├── ViewRule.jsx
    └── edit/
        └── EditRule.jsx    // dedicated edit page
```

The prefix is the same either way — only the location changes based on whether the form is a page or a dialog.

## Storybook titles

Storybook story titles include the component filename and must be kept in sync:

```jsx
// ListStatements.stories.js
import ListStatements from './ListStatements';

export default {
  title: 'app/orgs/[o_id]/statements/ListStatements',
  component: ListStatements,
};
```

## Examples

From `apps/statements.finopsbricks.com`:

```
src/app/orgs/[o_id]/
├── statements/
│   ├── ListStatements.jsx
│   └── [s_id]/
│       └── ViewStatement.jsx
├── accounts/
│   ├── ListAccounts.jsx
│   └── [acc_id]/
│       └── ViewAccount.jsx
├── rules/
│   ├── ListRules.jsx
│   ├── _components/
│   │   └── CreateRule.jsx       // dialog
│   └── [r_id]/
│       ├── ViewRule.jsx
│       └── edit/
│           └── EditRule.jsx     // dedicated edit page
└── settings/apikeys/
    ├── ListAPIKeys.jsx
    └── create/
        └── CreateAPIKey.jsx     // dedicated create page
```

## When the prefix does NOT apply

- **Shared/reusable components**: use PascalCase with no action prefix (`Button.jsx`, `DataTable.jsx`).
- **Tab or section widgets inside a detail page**: use a descriptive suffix like `Tab` or `Section` instead of a CRUD prefix (`RateCardsTab.jsx`, `TransactionsSection.jsx`). These are not CRUD pages.
- **Report pages**: use the `View` prefix (`ViewBalanceReport.jsx`, `ViewCashflowReport.jsx`) — reports are a read-only detail view of aggregated data.

## Related Notes
- [Component Files: PascalCase.jsx](/naming/files-components.md)
- [Components: PascalCase](/naming/components-pascalcase.md)
- [Container/Presentational Pattern](/architecture/components/container-presentational-pattern.md)
