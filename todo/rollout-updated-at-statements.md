# Statements: roll out `updated_at` + trigger

## Current
Core models `Accounts`, `Checks`, `Statements`, `Transactions` have no `updated_at`. `Rules` uses Sequelize auto-timestamps (`timestamps: true, createdAt: false, updatedAt: 'updated_at'`). `Api_keys` declares camelCase `createdAt`/`updatedAt` attributes with no `field` mapping. Manual writes: `src/components/Navbar/Sidebar/action.js:98`, `src/app/orgs/[o_id]/settings/feature_flags/action.js:23`, `src/app/orgs/[o_id]/settings/webhooks/action.js:21`.

## Proposed
One migration: `set_updated_at()`; add `updated_at TIMESTAMPTZ NOT NULL DEFAULT now()` to `accounts`, `checks`, `statements`, `transactions` (backfill from `created_at`); triggers on those plus `rules`, `api_keys`, `org_settings`. Switch `Rules` to explicit attrs + `timestamps: false`; fix `Api_keys` attribute names. Delete the three manual writes. Expose `updated_at` in each resource's public fields with `updated_at_from`/`updated_at_to` + `sort_by`.

## Why
[Required Columns](/architecture/database/required-columns.md), [Updated-at Trigger](/architecture/database/updated-at-trigger.md). Statements is the system of record other apps sync from.

## Files to Update
- `src/database/models/core/{Accounts,Checks,Statements,Transactions,Rules}.model.js`, `supporting/Api_keys.model.js`
- `scripts/db-setup.sql` + a dated migration
- the three action files above; `src/app/api/v1/{accounts,statements,transactions,rules}/route.js`
