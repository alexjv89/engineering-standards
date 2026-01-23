# MDX Library Choice for Next.js

When rendering markdown content in Next.js applications, choose the right library based on your needs.

## Decision Tree

1. **Need JSX components in markdown?** → Use MDX
2. **Only standard markdown?** → Use `react-markdown`
3. **MDX files in app/ directory?** → Use `@next/mdx`
4. **MDX files outside app/ (or from CMS)?** → Use `next-mdx-remote`

## Library Comparison

| Package | JSX Support | Use Case | Weight |
|---------|-------------|----------|--------|
| `react-markdown` | No | Standard markdown only | Light |
| `@mdx-js/mdx` | Yes | Core compiler (low-level) | Medium |
| `@next/mdx` | Yes | .mdx files in app/pages | Medium |
| `next-mdx-remote` | Yes | Dynamic content from files/CMS | Medium |

## Practical Trade-offs (Tested)

We tested both `@next/mdx` and `next-mdx-remote` for a docs site. Here's what we found:

### @next/mdx

**Pros:**
- Simpler setup - MDX files ARE pages
- Automatic routing - file path = URL
- Fewer dependencies (no gray-matter needed)
- Less code to maintain (no content loader, no dynamic route)

**Cons:**
- No frontmatter support out of the box
- Navigation must be hardcoded (manual sidebar updates)
- Doesn't work with turbopack (slower dev builds)
- Content mixed with app code

### next-mdx-remote

**Pros:**
- Frontmatter support (title, description, order)
- Auto-generated navigation from file metadata
- Content separate from app code
- Works with turbopack
- Easier to swap to CMS later

**Cons:**
- More code (content loader utility, dynamic catch-all route)
- More dependencies (gray-matter, next-mdx-remote)
- MDX parsing can be finicky (special characters like `>=` in tables cause issues)

### Side-by-Side Comparison

| Aspect | @next/mdx | next-mdx-remote |
|--------|-----------|-----------------|
| Setup complexity | Lower | Higher |
| Navigation | Manual/hardcoded | Can be auto-generated |
| Frontmatter | No | Yes |
| Turbopack compatible | No | Yes |
| Content location | In app/ | Anywhere |
| Code required | Config only | Loader + dynamic route |

### When to Choose Which

**Choose @next/mdx when:**
- Small docs site (~10 pages)
- Okay with manual navigation updates
- Don't need frontmatter metadata
- Want simplest possible setup

**Choose next-mdx-remote when:**
- Larger docs site with many pages
- Want auto-generated navigation
- Need frontmatter for metadata (title, order, etc.)
- Plan to migrate to CMS later
- Using turbopack for dev builds

## When to Use Each

### react-markdown

Use when you only need standard markdown without custom React components:

```jsx
import ReactMarkdown from 'react-markdown';
import remarkGfm from 'remark-gfm';

function MarkdownContent({ content }) {
  return (
    <ReactMarkdown remarkPlugins={[remarkGfm]}>
      {content}
    </ReactMarkdown>
  );
}
```

**Supports:** Tables, code blocks, links, lists, blockquotes (with remark-gfm)
**Does NOT support:** `<Callout>`, `<Tabs>`, or any JSX in markdown

### next-mdx-remote

Use for documentation sites or content that needs React components embedded in markdown:

```jsx
import { MDXRemote } from 'next-mdx-remote/rsc';
import remarkGfm from 'remark-gfm';

const components = {
  Callout: ({ type, children }) => (
    <div className={`callout callout-${type}`}>{children}</div>
  )
};

async function DocPage({ content }) {
  return (
    <MDXRemote
      source={content}
      components={components}
      options={{ mdxOptions: { remarkPlugins: [remarkGfm] } }}
    />
  );
}
```

**Content file (.mdx):**
```mdx
# Documentation

Standard markdown works here.

<Callout type="warning">
This renders as a React component!
</Callout>
```

### @next/mdx

Use when .mdx files live directly in your app/pages directory:

```javascript
// next.config.mjs
import createMDX from '@next/mdx';
import remarkGfm from 'remark-gfm';

const withMDX = createMDX({
  options: {
    remarkPlugins: [remarkGfm],
  },
});

export default withMDX({
  pageExtensions: ['js', 'jsx', 'mdx']
});
```

Also requires `mdx-components.jsx` at project root:

```jsx
import MDXComponents from '@/components/docs/MDXComponents';

export function useMDXComponents(components) {
  return { ...MDXComponents, ...components };
}
```

Then create `app/docs/page.mdx` directly.

### @mdx-js/mdx

The core compiler - rarely used directly. Use higher-level integrations instead.

## File Structure Examples

**@next/mdx:**
```
app/
  docs/
    page.mdx                    ← /docs
    authentication/
      page.mdx                  ← /docs/authentication
    endpoints/
      transactions/
        page.mdx                ← /docs/endpoints/transactions
```

**next-mdx-remote:**
```
content/
  docs/
    index.mdx                   ← Has frontmatter with order, title
    authentication.mdx
    endpoints/
      transactions.mdx
app/
  docs/
    page.jsx                    ← Renders index.mdx
    [...slug]/
      page.jsx                  ← Dynamic route loads any .mdx
lib/
  docs.js                       ← Content loader utility
```

## Related Notes
- [Component Architecture](/architecture/components/index.md)
