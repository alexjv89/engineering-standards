# Storybook Story Title Convention

Convention for naming story titles that mirror folder structure.

## Standard Convention

**Rule**: Match the folder structure exactly, lowercase `components/`

```javascript
// Component at: src/components/DatePicker.jsx
title: 'components/DatePicker'

// Component at: src/components/Filters/FilterField.jsx
title: 'components/Filters/FilterField'

// Page component at: src/app/orgs/[o_id]/transactions/_components/TransactionsTable.jsx
title: 'app/orgs/[o_id]/transactions/TransactionsTable'
```

**Rationale**: Titles should mirror the actual folder structure for consistency and easy navigation in Storybook's sidebar.

## Multi-User Story Files

When component behavior differs by user type, create separate story files:

```javascript
// File: ComponentName.stories.js
const meta = {
  title: 'components/ComponentName/AppUser',
  // ... app user configuration
};

// File: ComponentName.StaffUser.stories.js
const meta = {
  title: 'components/ComponentName/StaffUser',
  // ... staff user configuration
};
```

## Related Notes
- [Story File Organization](/storybook/organization/file-structure.md) - File structure order
- [Story Naming](/storybook/organization/story-naming.md) - User journey naming conventions
- [Component Type Guidelines](/storybook/component-type-guidelines.md) - Component categorization
