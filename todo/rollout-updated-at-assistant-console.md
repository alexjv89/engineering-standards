# Assistant + Console: roll out `updated_at` + trigger

## Current
**assistant** has no core models; its only manual write is `src/app/orgs/[o_id]/settings/feature_flags/action.js:23` (`org_settings`). **console** sets a global `define: { timestamps: true, createdAt: 'created_at', updatedAt: 'updated_at' }` (`src/database/sequelize.js:37-42`); `Orgs`/`Members`/`Roles`/`Invitations` use it, while the four better-auth tables (`Accounts`, `Sessions`, `Users`, `Verifications`) override with `timestamps: false` and are never written — `updated_at` there is stale by construction. The better-auth adapter renames `updated_at` → `updatedAt` on read (`src/database/better-auth-sequelize-adapter.js:103-105`).

## Proposed
assistant: `set_updated_at()` + triggers on `api_keys`, `org_settings`, `user_settings`; delete the one manual write. console: drop the global `define` mapping, declare the columns explicitly on all eight models, add triggers on all eight tables; confirm the better-auth adapter's rename still holds.

## Why
[Required Columns](/architecture/database/required-columns.md), [Updated-at Trigger](/architecture/database/updated-at-trigger.md), and `sequelize-initialization.md` no longer permits the global mapping.

## Files to Update
- assistant: `scripts/db-setup.sql`, the action file
- console: `src/database/sequelize.js`, `src/database/models/*.js`, `src/database/better-auth-sequelize-adapter.js`, `scripts/db-setup.sql`
