# Ledger: roll out `updated_at` + trigger

## Current
`Accounts.updated_at` and `Transactions.updated_at` are nullable with no default (`scripts/db-setup.sql:96,133`) and maintained by hand: `src/utils/ledger/writeAccount.js:160,164`, `src/utils/ledger/updateTransactionStatus.js:69,73`, `src/utils/ledger/replaceDraftEntries.js:80`, `src/app/api/v1/transactions/[id]/route.js:143`, `src/app/orgs/[o_id]/settings/feature_flags/action.js:23`. `Entries` deliberately has no `updated_at` (`replaceDraftEntries.js:16`: an entry is never modified).

## Proposed
Migration: `set_updated_at()`; `ALTER … SET NOT NULL, SET DEFAULT now()` after `UPDATE … SET updated_at = COALESCE(updated_at, created_at)`; triggers on `accounts`, `transactions`, `api_keys`, `org_settings`. Delete the seven manual writes. Keep `Entries` as the documented append-only exception (add the model comment).

## Why
[Required Columns](/architecture/database/required-columns.md), [Updated-at Trigger](/architecture/database/updated-at-trigger.md).

## Files to Update
- `src/database/models/core/{Accounts,Transactions,Entries}.model.js`
- `scripts/db-setup.sql` + a dated migration
- the five source files above
