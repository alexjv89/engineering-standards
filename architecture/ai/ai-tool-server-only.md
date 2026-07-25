# AI Tool Server-Only Pattern

Enforce server-side execution of AI tools by importing "server-only" at the top of tool files.

## Pattern Overview

AI tools run server-only because they access sensitive resources:

- Authentication cookies
- Backend services
- Database connections

The "server-only" import prevents accidental client-side bundling.

## Implementation

```javascript
import "server-only";
import { tool } from "ai";
import { z } from 'zod';
import * as vimanaSVC from "@/utils/vimanaSVC";

export function generateGetMetrics({ cookieStore, p_id }) {
  const toolDefinition = tool({
    description: "Get production metrics for devices",
    inputSchema: z.object({
      metrics: z.array(z.string()),
      start: z.string().datetime({ offset: true }),
      end: z.string().datetime({ offset: true }),
    }),
    execute: async (input) => {
      // Has access to cookieStore (server-only)
      const data = await vimanaSVC.getMetrics({
        cookieStore,
        plant: p_id,
        ...input
      });
      return data;
    },
  });
  return toolDefinition;
}
```

## Why This Matters

1. **Security**: Tools access authenticated backend APIs via cookies
2. **Architecture**: Prevents confusion about where code executes
3. **Build Safety**: Next.js build fails if server-only code is imported client-side
4. **Performance**: Keeps server logic out of client bundles

## What "server-only" Does

The `server-only` package causes build errors if the importing file is used in client components:

```javascript
// ❌ This will fail at build time
"use client";
import { generateGetMetrics } from "@/tools"; // Error: Cannot import server-only module

// ✅ This works - server component
import { generateGetMetrics } from "@/tools"; // OK
```

## Common Server-Only Resources in Tools

- **Authentication**: `cookieStore` from Next.js headers
- **Direct API Calls**: Backend service integrations
- **Database Access**: Direct database queries
- **Environment Variables**: Server-only secrets

## Related Notes

- [AI Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md) - Tool structure
- [AI Tool Service Abstraction](/architecture/ai/ai-tool-service-abstraction.md) - Backend service integration
