# Utility Files: camelCase in Apps, kebab-case in Factories

Utility and helper files in apps use `camelCase`. Factory and worker repos use `kebab-case` (`statement-rename-script.js`, `email-filing.js`). Follow the repo you are in; don't rename across the split.

## Pattern

```
userService.js
fetchAPI.js
dateFormatter.js
stringUtils.js
```

## Consistency

Apply camelCase consistently throughout the project:

```
// Good - consistent camelCase
src/utils/
├── fetchAPI.js
├── dateFormatter.js
├── stringUtils.js

src/services/
├── userService.js
├── authService.js
├── apiService.js
```

## Why camelCase?

- Matches function naming convention (functions are camelCase)
- Simpler for modules that export functions
- Fewer characters than kebab-case
- Consistent with JavaScript naming patterns

## Named Exports Typical

Utility files typically use named exports:

```javascript
// stringUtils.js
export function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

export function truncate(str, max_length) {
  return str.length > max_length ? str.slice(0, max_length) + '...' : str;
}
```

## Related Notes
- [Files: Components](/naming/files-components.md)
- [Files: Tests](/naming/files-tests.md)
- [Utility Modules](/architecture/modules/utility-modules.md)
