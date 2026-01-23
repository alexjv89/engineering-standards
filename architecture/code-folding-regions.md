# Code Folding with Region Comments

Use `#region` / `#endregion` comments to create foldable sections in your code for better navigation and bird's-eye view of file structure.

## Pattern

```javascript
//#region Section Name
function helperOne() { ... }
function helperTwo() { ... }
function helperThree() { ... }
//#endregion

//#region Another Section
function anotherHelper() { ... }
//#endregion
```

When folded, you see only the section names, giving you an overview of the file's structure.

## When to Use

- Files with **many helper functions** that can be logically grouped
- **Long files** where navigation is difficult
- Code with **clear functional boundaries** (validation, transformation, extraction, etc.)

## Example: Utility Module

```javascript
const _ = require('lodash');

//#region Validation Helpers
function isValidEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

function isValidPhone(phone) {
  return /^\+?[\d\s-]{10,}$/.test(phone);
}

function isNonEmpty(value) {
  return value !== null && value !== undefined && value !== '';
}
//#endregion

//#region Formatting Helpers
function formatCurrency(amount, currency = 'USD') {
  return new Intl.NumberFormat('en-US', { style: 'currency', currency }).format(amount);
}

function formatDate(date, format = 'YYYY-MM-DD') {
  return moment(date).format(format);
}
//#endregion

//#region Data Transformation
function normalizeUser(raw_user) {
  return {
    id: raw_user.id,
    name: _.trim(raw_user.name),
    email: raw_user.email?.toLowerCase(),
  };
}
//#endregion

module.exports = { isValidEmail, isValidPhone, formatCurrency, formatDate, normalizeUser };
```

## IDE Support

Most modern editors support region folding:

| Editor | Fold All Regions | Unfold All |
|--------|------------------|------------|
| VS Code | `Cmd+K Cmd+8` | `Cmd+K Cmd+9` |
| WebStorm | `Cmd+Shift+-` | `Cmd+Shift++` |
| Vim | `zM` (with foldmethod) | `zR` |

## Benefits

- **Quick overview**: Collapse all regions to see file structure at a glance
- **Focused editing**: Expand only the section you're working on
- **Code reviews**: Reviewers can navigate large files efficiently
- **Self-documenting**: Region names describe what each section does

## Guidelines

- **Group related functions**: Each region should contain functions with similar purpose
- **Use descriptive names**: Region name should clearly indicate contents
- **Don't over-use**: Only use regions when files have natural groupings
- **Keep regions balanced**: Aim for 3-7 functions per region

## Related Notes

- [Arrow Functions vs Declarations](/architecture/arrow-vs-declaration.md)
- [Pure Functions](/architecture/functional/pure-functions.md)
