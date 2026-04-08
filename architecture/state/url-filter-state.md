# URL as Filter State

For list pages with filters, search, sort, or pagination, store filter state in the URL query string instead of (or in addition to) `useState`. The URL becomes part of the application state, which makes filters shareable, reload-survivable, and back-button-friendly.

Two patterns are supported. Pick based on data scale and where filtering needs to happen.

## Why URL state

Plain `useState` for filters has three failure modes:

1. **Not shareable** — you can't send a teammate "all unpaid invoices for customer X"
2. **Lost on reload** — refresh blows away the filter
3. **Lost on back-button** — navigate into a detail page, come back, filters are gone

Storing the same state in `?status=unpaid&customer_id=...` fixes all three.

## Pattern A: Hybrid (URL state + client filter)

**Use when** the full dataset is small enough to load up front (≤ a few thousand rows) and filtering can happen in memory.

The server fetches everything once. The client owns filter state via `useState`, mirrors changes to the URL via `window.history.replaceState`, and filters in `useMemo`. **No server round-trip on filter change** — the URL updates but no navigation occurs.

```javascript
// src/hooks/useUrlFilters.js
"use client";
import { useCallback, useEffect, useState } from "react";

export default function useUrlFilters(initial_values, defaults = {}) {
  const [filters, setFilters] = useState(initial_values);

  useEffect(() => {
    if (typeof window === "undefined") return;
    const next = new URLSearchParams();
    for (const [key, value] of Object.entries(filters)) {
      const default_value = defaults[key] ?? "";
      if (value != null && value !== "" && value !== default_value) {
        next.set(key, String(value));
      }
    }
    const qs = next.toString();
    const new_url = qs
      ? `${window.location.pathname}?${qs}`
      : window.location.pathname;
    if (new_url !== window.location.pathname + window.location.search) {
      window.history.replaceState(null, "", new_url);
    }
  }, [filters, defaults]);

  const setFilter = useCallback((key, value) => {
    setFilters((prev) => ({ ...prev, [key]: value }));
  }, []);

  const clearFilters = useCallback(() => {
    setFilters(
      Object.fromEntries(
        Object.keys(initial_values).map((k) => [k, defaults[k] ?? ""]),
      ),
    );
  }, [initial_values, defaults]);

  return { filters, setFilter, clearFilters };
}
```

The page (Next.js server component) reads `searchParams` once and seeds the client:

```javascript
// page.jsx (server component)
export default async function Page({ searchParams }) {
  const resolved_search = (await searchParams) || {};
  const records = await db.Records.findAll({ where: { org: org.id } });

  const initial_filters = {
    q: resolved_search.q ?? "",
    status: resolved_search.status ?? "all",
  };

  return <ListRecords records={records} initialFilters={initial_filters} />;
}
```

The client reads `filters.q`, `filters.status`, and runs the existing in-memory filter inside `useMemo` — unchanged from a `useState`-only implementation, except the source of truth is now the hook.

**Properties**:
- Zero network on filter change
- Filtering remains instant
- URL updates without triggering Next.js navigation (`replaceState` skips the router)
- Reload, share, and back-button all work

## Pattern B: Server fetch (URL state + server filter)

**Use when** the dataset is too large to load up front, the database is doing the work anyway (proper indexes, server-side pagination), or filters drive expensive joins.

The client writes filters to the URL via `router.push`, which **does** trigger a server re-render. The server reads `searchParams`, builds a `WHERE` clause via a dedicated `filterFoo.js` utility, and re-fetches the page.

```javascript
// src/utils/filter/filterTransactions.js
import "server-only";
import { Op } from "sequelize";

export function filterTransactions({ org, searchParams }) {
  const where = { org: org.id };
  if (searchParams.q) {
    where[Op.or] = [
      { description: { [Op.iLike]: `%${searchParams.q}%` } },
      { reference: { [Op.iLike]: `%${searchParams.q}%` } },
    ];
  }
  if (searchParams.status && searchParams.status !== "all") {
    where.status = searchParams.status;
  }
  return where;
}

// page.jsx
export default async function Page({ searchParams }) {
  const resolved_search = (await searchParams) || {};
  const where = filterTransactions({ org, searchParams: resolved_search });
  const transactions = await db.Transactions.findAll({ where, ...pagination });
  return <ListTransactions transactions={transactions} searchParams={resolved_search} />;
}

// client filter component
"use client";
import { usePathname, useRouter } from "next/navigation";

function StatusFilter({ searchParams }) {
  const router = useRouter();
  const pathname = usePathname();

  const setStatus = (value) => {
    const next = new URLSearchParams(searchParams);
    if (value === "all") next.delete("status");
    else next.set("status", value);
    router.push(`${pathname}?${next.toString()}`);
  };

  return <Select defaultValue={searchParams.status ?? "all"} onValueChange={setStatus} />;
}
```

**Properties**:
- Scales to arbitrarily large datasets — DB does the filtering
- Every filter change is a network round-trip + DB query + server render
- Text inputs **must** debounce or pagination/keystroke storms will hammer the server
- Pair with `useTransition` to render the spinner without blocking the input

## Decision matrix

| Question | Hybrid | Server fetch |
|---|---|---|
| Dataset size | Up to a few thousand rows | Unbounded |
| Latency on filter change | ~0ms (in-memory) | Network RTT + DB + render |
| Server round-trip per change | No | Yes |
| Text search needs debounce | No (instant) | Yes (~300ms) |
| URL is shareable / reload-safe | Yes | Yes |
| Code complexity | Lower (one hook) | Higher (filter utils + WHERE builders) |
| Migration path to scale | Swap inner `filter()` for server fetch later | Already scaled |

**Default to hybrid** for new list pages. Switch a page to server fetch only when the in-memory filter starts feeling slow or the up-front payload gets uncomfortably large.

## Common conventions

Regardless of which variant:

- **Short, lowercase param names**: `q`, `status`, `customer_id`, `period`. Drop `filter_` prefixes — they're noisy in URLs.
- **Default values are omitted from the URL**: `status=all` should not appear in the URL; leaving it out keeps URLs clean.
- **Server reads `searchParams` once** to seed initial state. After that, ownership lives where filtering lives (client for hybrid, server for fetch).
- **Sort and pagination follow the same pattern**: extend the same hook (or build a sibling `useUrlSort`) so all view state lives in the URL.

## Related Notes
- [State Management: Local vs Global](/architecture/state/state-management-local-vs-global.md)
- [State Colocation](/architecture/state/state-colocation.md)
- [Custom Hooks Pattern](/architecture/components/custom-hooks.md)
