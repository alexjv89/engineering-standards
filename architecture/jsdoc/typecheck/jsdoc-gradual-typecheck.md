# JSDoc: Gradual Type Checking with @ts-check

Configure jsconfig.json for opt-in type checking on a per-file basis using `@ts-check`.

## Why Gradual Adoption?

- Add type safety incrementally without converting entire codebase
- No blocking errors during builds
- Focus type checking on critical service layer first
- Same approach used by [SvelteKit](https://github.com/sveltejs/kit/discussions/4429)

## jsconfig.json Configuration

```json
{
  "compilerOptions": {
    "jsx": "react-jsx",
    "baseUrl": ".",
    "paths": { "@/*": ["./src/*"] },
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "allowJs": true,
    "checkJs": false,
    "noEmit": true,
    "strict": false,
    "skipLibCheck": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"]
  },
  "include": ["src/**/*", "src/types/**/*.d.ts"],
  "exclude": ["node_modules", ".next", "**/*.test.js"]
}
```

**Key settings:**
- `jsx: "react-jsx"` - React 17+ JSX transform (no React import needed)
- `checkJs: false` - Only files with `// @ts-check` are type-checked
- `resolveJsonModule: true` - Allow importing JSON files

## Enable Type Checking Per File

Add `// @ts-check` at the top of files you want checked:

```javascript
// @ts-check
import { fetchSVC } from '@/utils/fetchSVC';

/**
 * @typedef {import('@/types/svc').SVCDevice} SVCDevice
 */

/**
 * @param {Object} options
 * @param {Object} options.cookieStore
 * @param {string} options.plant
 * @returns {Promise<SVCDevice[]>}
 */
export async function getDevices({ cookieStore, plant }) {
  // TypeScript engine now checks this file
}
```

## NPM Scripts

```json
{
  "scripts": {
    "typecheck": "tsc -p jsconfig.json",
    "typecheck:watch": "tsc -p jsconfig.json --watch"
  }
}
```

## What Gets Checked

| File Type | Checked? |
|-----------|----------|
| Files with `// @ts-check` | Yes |
| Files without `// @ts-check` | No |
| Test files (`*.test.js`) | No (excluded) |
| node_modules | No |

## Adoption Strategy

1. Start with service layer (API calls, data fetching)
2. Add `@ts-check` as you modify files
3. Run `npm run typecheck` to verify
4. Expand to components and utilities over time

## Related Notes
- [JavaScript with JSDoc Principle](/principles/javascript-with-jsdoc.md)
- [JSDoc Centralized Types](/architecture/jsdoc/types/jsdoc-centralized-types.md)
- [JSDoc Importing Types](/architecture/jsdoc/syntax/jsdoc-imports.md)
- [Type Checking Tools (VS Code vs CLI)](/architecture/jsdoc/typecheck/jsdoc-typecheck-tools.md)
- [When to Disable @ts-check](/architecture/jsdoc/typecheck/jsdoc-disable-tscheck.md)
