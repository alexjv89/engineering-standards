# Model Organization: Core vs Supporting

Split models into `core/` (the app's domain) and `supporting/` (cross-cutting infrastructure
and config), and mirror the split in Postgres schemas (`core.*`, `supporting.*`).

## Folder Pattern

```
src/database/models/
├── core/                          # domain — the app's reason to exist
│   ├── Transaction.model.js
│   ├── ParsedEmail.model.js
│   └── MailboxConnection.model.js
└── supporting/                    # cross-cutting infra & config
    ├── Api_keys.model.js
    ├── EventLogs.model.js
    ├── Tags.model.js
    ├── EntityTags.model.js
    └── OrgSetting.model.js
```

## Core vs Supporting

**Discriminator**: *core = a model that has (or will have) a CRUD API resource/endpoint.*

| | Core | Supporting |
|---|---|---|
| Role | The app's domain — its reason to exist | Cross-cutting infra & config |
| CRUD API | Yes (or planned) | No |
| Examples | `Transaction`, `Invoice`, `Recording` | `Api_keys`, `EventLogs`, `Tags`, `EntityTags`, `OrgSetting` |

**Supporting models need not be identical across apps.** This is a reference standard, not a
shared abstraction — each app owns its copy and may extend it (add supporting models, or
customize one where it must reference domain, e.g. `EntityTags.entity_type`'s `isIn` enum).
Start from the `app-template` copy; diverge only where the app requires it.

**App-unique ≠ supporting.** The tempting mistake is to reason "this table is specific to one app
and will never be shared, so it's supporting." Uniqueness is irrelevant — the test is *resource
vs. plumbing*. A model can be 100% app-specific and still be **core** if the app exposes it as a
resource. Worked example: orchestrator's `WorkerSessions` is entirely orchestrator-specific, yet
it's **core** — orchestrator exposes Workers via `/api/v1/worker-sessions` and a Workers page, so
it *has a CRUD API resource*. Contrast `EventLogs`: also per-app, but no resource → supporting.
The mirror image is an auth service's `sessions` table: auth-unique, central to the app, but pure
plumbing (auto-created, no managed resource) → supporting.

## Postgres Schemas

Realize the split at the DB layer — `core.*` and `supporting.*`, alongside the existing
`pgboss` schema — instead of piling everything into `public`. Attach the schema in each
model's options:

```javascript
// supporting/EventLogs.model.js
sequelize.define('EventLogs', { /* ... */ }, {
  tableName: 'event_logs',
  schema: 'supporting',
});
```

- **Cross-schema joins are free** — `SELECT … FROM core.transactions t JOIN supporting.tags g`
  works with no penalty; foreign keys and transactions span schemas normally.
- **`search_path`** — set it on the connection so unqualified names (raw SQL, associations)
  resolve; prefer qualifying names in raw SQL for clarity:

  ```javascript
  new Sequelize(process.env.DB_APP, {
    dialectOptions: { options: '-c search_path=core,supporting,public' },
  });
  ```
- **`db-setup.sql`** — `CREATE SCHEMA core; CREATE SCHEMA supporting;` then schema-qualify each
  `CREATE TABLE`. Leave `pgboss` untouched.
- **Migration** — move an existing table with `ALTER TABLE <name> SET SCHEMA <schema>;`.

## Why This Convention?

- **Readable on disk and in the DB** — the domain/infra boundary is visible in the file tree,
  in queries (`core.transactions`), and in DB GUIs (grouped by schema).
- **Standardizable** — supporting models are a recognizable, template-seeded pattern.
- **No coupling cost** — schemas are namespaces, not isolation; joins and FKs are unaffected.

## Related Notes
- [Settings Models](/architecture/database/settings-models.md) - Settings as supporting JSONB models
- [Required Columns](/architecture/database/required-columns.md) - `id`, `org`, `created_at`, `updated_at` on every core model
- [Table Naming](/architecture/database/table-naming.md) - snake_case table names within a schema
- [Model File Naming](/architecture/database/model-file-naming.md) - `{ModelName}.model.js`
- [Foreign Key Pattern](/architecture/database/foreign-key-pattern.md) - FKs (incl. cross-schema)
