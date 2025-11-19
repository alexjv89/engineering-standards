# Coverage Requirements

What counts as a component and what counts as "covered" for Storybook coverage tracking.

## Coverage Goal

**Target**: 100% coverage

**Definition**: Every component has at least one corresponding story file.

**Enforcement**: Coverage script exits with error code 1 if < 100% (for CI integration).

## What Counts as a Component?

### Included

Files that should have stories:

- **React components** (`.jsx`, `.tsx`)
- **Exported functions that return JSX**
- **Default exports** (`export default Button`)
- **Named exports** (`export function Card() { ... }`)

**Example**:
```javascript
// Button.jsx - NEEDS STORY
export default function Button({ children }) {
  return <button>{children}</button>;
}
```

### Excluded

Files that don't need stories:

- **Utilities and helpers** (should use `.js`, not `.jsx`)
- **Custom hooks** (use `.js`, document separately)
- **Type definitions** (`.ts`, `.d.ts`)
- **Constants and config** (use `.js`)
- **Test files** (`*.test.jsx`, `*.spec.jsx`)
- **Next.js special files** (`page.jsx`, `layout.jsx`, `loading.jsx`, `error.jsx`, `not-found.jsx`)

**Example**:
```javascript
// utils/formatDate.js - NO STORY NEEDED (not a component)
export function format_date(date) {
  return date.toLocaleDateString();
}
```

## What Counts as "Covered"?

A component has coverage if either:

### Option 1: Has Matching Story File

A `.stories.js` or `.stories.jsx` file exists with matching name in **same directory**.

**Examples**:
- `Button.jsx` + `Button.stories.jsx` = ✅ Covered
- `components/Card.jsx` + `components/Card.stories.jsx` = ✅ Covered
- `Button.jsx` + `stories/Button.stories.jsx` = ❌ Not covered (different directory)

**Name matching rules**:
- Exact match before `.jsx` / `.stories.jsx`
- Case-sensitive (`button.jsx` ≠ `Button.jsx`)
- Story can be `.stories.js` or `.stories.jsx`

### Option 2: Explicitly Excluded

Component is in exclusion patterns in `scripts/storybook-coverage.js`.

**Not recommended**: Better to rename non-components to `.js` instead of excluding.

## File Extension Rules

| File Type | Extension | Needs Story? |
|-----------|-----------|--------------|
| React component | `.jsx` or `.tsx` | ✅ Yes |
| Utility function | `.js` or `.ts` | ❌ No |
| Custom hook | `.js` | ❌ No |
| Type definition | `.ts`, `.d.ts` | ❌ No |
| Test file | `.test.jsx` | ❌ No (auto-excluded) |
| Story file | `.stories.jsx` | ❌ No (auto-excluded) |

**Rule of thumb**: If it doesn't return JSX, use `.js` not `.jsx`.

## Common Misclassifications

### Utility Flagged as Component

**Problem**:
```javascript
// src/utils/helpers.jsx - Uses .jsx but not a component
export function calculate_total(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}
```

**Coverage script**: ❌ Missing story for `helpers.jsx`

**Fix**: Rename to `.js`
```bash
mv src/utils/helpers.jsx src/utils/helpers.js
```

### Hook Flagged as Component

**Problem**:
```javascript
// src/hooks/use_auth.jsx - Uses .jsx but is a hook
export function use_auth() {
  const [user, set_user] = useState(null);
  // ...
}
```

**Fix**: Rename to `.js`
```bash
mv src/hooks/use_auth.jsx src/hooks/use_auth.js
```

### Next.js Page Flagged as Component

**Problem**:
```javascript
// src/app/dashboard/page.jsx - Next.js route
export default function DashboardPage() {
  return <div>Dashboard</div>;
}
```

**Coverage script**: Auto-excluded (no story needed)

**Reason**: Next.js special files are excluded by pattern.

## Enforcement Strategy

### Current

**Manual**: Developers run `npm run storybook:coverage` before committing.

**Exit code**: Script exits with code 1 if < 100%, but not enforced in CI yet.

### Future (Recommended)

**CI Enforcement**: Add to GitHub Actions:
```yaml
- name: Check Coverage
  run: npm run storybook:coverage
```

Build fails if coverage < 100%.

**Pre-commit hook**: Block commits if coverage drops:
```bash
npm run storybook:coverage || exit 1
```

## Related Notes

- [Coverage Script](/storybook/testing/coverage-script.md) - How script scans and matches
- [Coverage Reports](/storybook/testing/coverage-reports.md) - Understanding coverage output
- [User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - How to write stories
