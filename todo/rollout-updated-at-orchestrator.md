# Orchestrator: roll out `updated_at` + trigger

## Current
Core models `ProcessWatchers`, `Stations` (table `processes`), `StepQueue`, `SupportingDocuments`, `WorkRecords` declare no `updated_at`; `scripts/db-setup.sql:68,83,93` create the column on `processes`, `step_queue`, `work_records` anyway, so it exists in the DB, is invisible to the ORM, and holds its insert-time value forever. `WorkerSessions` uses `started_at`/`last_seen_at`/`ended_at` (a legitimate domain-modelled exception — record it in the model). Manual writes: `src/components/Navbar/Sidebar/action.js:86`, `src/app/orgs/[o_id]/settings/feature_flags/action.js:23`, `src/app/orgs/[o_id]/stations/action.js:205`. Note the repo works on `develop`, not `main`.

## Proposed
Migration: `set_updated_at()`; add the column where missing, backfill from `created_at`; declare it in every core model; triggers on `processes`, `process_watchers`, `step_queue`, `supporting_documents`, `work_records`, `api_keys`, `org_settings`. Delete the three manual writes. Add `updated_at_from`/`updated_at_to` + `sort_by` to `work-records` and `stations` — the only app today with no date filtering at all.

## Why
[Required Columns](/architecture/database/required-columns.md), [Updated-at Trigger](/architecture/database/updated-at-trigger.md), [Date Range Filtering](/architecture/api/date-range-filtering.md).

## Files to Update
- `src/database/models/core/*.model.js`
- `scripts/db-setup.sql` (already drifted from the models) + a dated migration
- the three action files; `src/app/api/v1/{work-records,stations}/route.js`
