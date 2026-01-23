# Next.js generateMetadata and Authorization

Dynamic page titles using `generateMetadata` do not need policy/authorization checks because Next.js server rendering prevents unauthorized data from reaching the client.

## Why No Policy Check Needed

In Next.js App Router:

1. `generateMetadata` and the page component run on the server
2. If the page component redirects (via policy check), Next.js sends a redirect response
3. The HTML with metadata is never sent to the client
4. Authorization is enforced before any response reaches the browser

## Pattern

```javascript
// page.jsx
import isMember from "@/policies/isMember";
import { getDB } from "@/database";

// No policy check needed - page component handles authorization
export async function generateMetadata({ params }) {
  const resolved_params = await params;
  const db = getDB();
  const account = await db.Accounts.findOne({
    where: { id: resolved_params.acc_id },
    attributes: ['name'],
    raw: true,
  });
  return {
    title: account?.name || "View Account",
    description: "App description",
  };
}

// Page component handles authorization
export default async function Page({ params }) {
  const resolved_params = await params;
  const { org } = await isMember({ params: resolved_params }); // Redirects if unauthorized

  // ... rest of page logic
}
```

## Why This Is Safe

1. **Server-side streaming**: Next.js streams responses, but redirects interrupt the stream
2. **Redirect takes precedence**: When `isMember` calls `redirect()`, it throws a special error that Next.js catches
3. **No partial response**: The redirect response replaces any partial HTML that was being built
4. **No race condition**: Both functions run server-side; the client only receives the final response (redirect or full page)

## Anti-Pattern

Don't duplicate policy checks in `generateMetadata`:

```javascript
// Unnecessary - adds extra DB calls
export async function generateMetadata({ params }) {
  const resolved_params = await params;
  const { org } = await isMember({ params: resolved_params }); // Redundant
  const db = getDB();
  const account = await db.Accounts.findOne({
    where: { id: resolved_params.acc_id, org: org.id },
    attributes: ['name'],
    raw: true,
  });
  return { title: account?.name || "View Account" };
}
```

## When You Might Want Policy Check

Only add policy checks to `generateMetadata` if:

- The metadata itself contains sensitive information beyond what the URL reveals
- You want to show different metadata to authorized vs unauthorized users
- The page doesn't have its own policy check (unusual)

## Related Notes

- [Error Boundaries](./error-boundaries.md)
