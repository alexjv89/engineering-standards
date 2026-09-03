# Updated-at Trigger

`updated_at` is set by a `BEFORE UPDATE` trigger in Postgres, one per table, so it is correct for every write path — ORM, raw SQL, backfill, or a `psql` fix — and application code never touches it.

## Pattern

```sql
-- Once per database (scripts/db-setup.sql)
CREATE OR REPLACE FUNCTION set_updated_at() RETURNS trigger AS $$
BEGIN
  IF row(NEW.*) IS DISTINCT FROM row(OLD.*) THEN
    NEW.updated_at = now();
  END IF;
  RETURN NEW;
END $$ LANGUAGE plpgsql;

-- Once per table that has updated_at
CREATE OR REPLACE TRIGGER core_transactions_set_updated_at
  BEFORE UPDATE ON core.transactions
  FOR EACH ROW EXECUTE FUNCTION set_updated_at();
```

Trigger name: `{schema}_{table}_set_updated_at`. The function lives in `public`.

## Adding the column to an existing table

```sql
BEGIN;
ALTER TABLE core.transactions
  ADD COLUMN IF NOT EXISTS updated_at TIMESTAMPTZ NOT NULL DEFAULT now();
UPDATE core.transactions SET updated_at = created_at;   -- "never changed" == created
CREATE OR REPLACE TRIGGER core_transactions_set_updated_at
  BEFORE UPDATE ON core.transactions
  FOR EACH ROW EXECUTE FUNCTION set_updated_at();
COMMIT;
```

Backfill runs **before** the trigger is attached, so the backfill itself doesn't stamp every row with the migration time. The same migration deletes any `updated_at: new Date()` the app was writing by hand — leaving both in place is two mechanisms for one fact.

## Why a trigger, and why the guard

- **Manual maintenance fails.** A survey of ten apps found `updated_at` present but never written in most of them; the only reliable writers were a handful of hand-placed `new Date()` calls that new code paths forgot.
- **ORM hooks miss writes.** Sequelize `timestamps: true` covers `instance.update()` and `Model.update()`, not `sequelize.query()`, bulk backfills, or migrations.
- **`IS DISTINCT FROM`**: a save that changes nothing must not look like an edit. Sync consumers filter on `updated_at_from`; a no-op `UPDATE` would otherwise make them re-process the row.

## Related Notes

- [Timestamp Columns](/architecture/database/timestamp-columns.md) — the column declaration this maintains
- [Required Columns](/architecture/database/required-columns.md) — which tables need the trigger
- [Date Range Filtering](/architecture/api/date-range-filtering.md) — `updated_at_from` / `updated_at_to`, the consumer that depends on this being true
