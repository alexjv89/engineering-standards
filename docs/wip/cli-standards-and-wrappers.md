# CLI Standards & the `fob-<tool>` Wrapper Family

## Status: IN PROGRESS (~30%)

Define a documented CLI standard (not a shared framework) and use it to build a family of
thin, consistent command-line wrappers — `fob-<tool>` — over both FinOpsBricks apps and
external SaaS tools (QuickBooks Online, Zoho Books, …). Each wrapper is a **2-in-1 repo**:
an importable client library *and* a CLI over the same functions, so an FDE can prototype in
the terminal and ship the exact same call into a worker. A git-style subcommand dispatcher
gives a single `fob <tool> …` entry point without coupling the wrappers together.

---

## Problem Statement

**The vision.** As an FDE org we constantly fetch and push data across many finance tools.
Third-party CLIs are inconsistent (a decent unofficial `qbo-cli` exists; no Zoho Books CLI
exists at all) and each has its own grammar, output, and auth quirks. Relying on a grab-bag
of external CLIs is a tax on every engagement. We want to own a **consistent** set of
wrappers so FDEs prototype and get to production fast.

**Current state that blocks this:**

- **No documented CLI standard.** The good conventions live *inside* one CLI
  (`cli-fob/docs/cli-design-style.md`) and in the heads of whoever built `cli-fobs`. Nothing
  repo-neutral exists in engineering-standards, so a new `fob-qbo` has nothing to build against.
- **The API wrapper for statements is maintained twice.** `@fob/lib-worker-statements`
  (workers import it) and `cli-fobs`'s `src/utils/http.js` + `cli/statements/**` (the CLI)
  each re-implement the same endpoints, pagination, and auth. Fixes must be made in two places.
- **`lib-email` proved a good shape but a weak CLI.** It pioneered the dual "library import
  *or* CLI" pattern, but its CLI is a hand-rolled `switch`/`parse()` — none of the yargs
  grammar, per-level `--help`, or output conventions that make `fob`/`fobs` intuitive.
- **No unified entry point.** Even if we build `fob-qbo`, `fob-zb`, `fob-stm` as separate
  binaries, there's no `git`-like `fob <tool> …` front door.

**Desired outcome:** a written standard + a growing set of `fob-<tool>` wrappers that all feel
identical, share proven code by **copy (not a package)**, expose an importable client for
workers, and are reachable through one dispatching entry point.

## Proposed Solution

Decisions locked in during brainstorming (2026-07-25):

1. **Standard as documentation, NOT a `cli-kit`/generator.** Consistency comes from a written
   spec + copying proven files (`format.js`, `safe()`, the yargs skeleton, an auth pattern)
   into each new CLI + review against the spec. This is the same "copy, don't extract" trade
   `sor-cli-convergence.md` already made for `format.js`/`http.js`. Re-evaluate extracting a
   shared lib only if we ever pass ~15 wrappers and are fixing the same bug repeatedly.

2. **Each tool = one standalone 2-in-1 repo.** `@fob/<tool>` exports a client (workers
   `import { getInvoices } from '@fob/qbo'`) *and* ships a `fob-<tool>` binary built on the
   same functions. This is the `lib-email` shape done right — it's what makes prototype→worker
   a copy-paste, not a rewrite.

3. **Grammar + infra lifted from what already works:**
   - Grammar from `cli-fob/docs/cli-design-style.md`: `resource action target options`,
     explicit verbs (no inference), `--json` everywhere, help at every level.
   - Infra conventions from `cli-fobs`: `safe()` error wrapper, `format.js` helpers, column
     selector, YAML creds file, exit codes.
   - Dual lib+CLI project shape from `lib-email` (keep the structure, drop the `parse()`).

4. **Auth is documented as patterns, not a plugin system.** The standard shows an api-key
   example and an OAuth2-refresh example (QBO/Zoho need the latter — refresh tokens, realm IDs,
   token expiry). Each wrapper implements its own; no shared auth package.

5. **Git-style subcommand dispatch.** A `fob` launcher resolves `fob <tool> …` to an installed
   `fob-<tool>` binary on `PATH` and execs it (exactly how `git foo` finds `git-foo`). Bare
   `fob` lists discovered wrappers. This gives one front door while every wrapper stays
   independently installed, versioned, and released — no aggregator that version-couples them.
   ⚠️ Naming collision to resolve: `cli-fob` already owns the `fob` binary (worker-context
   commands). Decide whether the dispatcher *is* an evolution of that `fob`, or the
   worker-context commands move under a dispatched `fob-worker`.

6. **Deprecate `@fob/lib-worker-statements` → `@fob/stm`.** Fold the statements client and the
   `cli-fobs` statements command subtree into one `fob-stm` wrapper. Workers import the client;
   the CLI wraps the same functions (resolving creds from its config and passing them via the
   existing `credentials` override param). Kills the double-maintenance.

7. **FinOpsBricks' own apps join the family (to confirm).** If uniformity across *all* wrappers
   is the goal, `statements`/`billing` become standalone `fob-stm`/`fob-bil` and the `fobs`
   aggregator's reason-to-exist shrinks. This partially reverses `sor-cli-convergence.md` — its
   "unify because creds are shared" argument was scoped to fob's internal apps and does **not**
   extend to external vendors (independent OAuth, no shared org model). **Needs an explicit
   decision — see Open Questions.**

## Open Questions (gate the structural phases)

- [ ] **Realistic wrapper count?** 2–3 → the standard + copying is plenty. 10+ → revisit a
      shared lib. This number sizes how much rigor the standard needs.
- [x] **Does `statements` actually go standalone `fob-stm`, retiring the `fobs` aggregator?**
      **DECIDED 2026-07-25: YES.** statements becomes a standalone `fob-stm` 2-in-1 wrapper;
      the `fobs` aggregator retires (billing/orchestrator follow the same path later). This
      confirms Decision 7 and partially reverses `sor-cli-convergence.md`.
- [ ] **Dispatcher naming:** does the new `fob` launcher absorb today's worker-context `fob`,
      or do those commands move to `fob-worker`? (Decision 5.)
- [ ] **Who maintains a wrapper after an engagement ends** — the org or the authoring FDE? This
      shapes monorepo vs scattered repos.
- [ ] **Vendor-faithful or normalized?** Do wrappers expose each tool's raw API shape, or
      normalize (e.g. QBO + Zoho invoices → one schema)? Normalization is a much bigger scope
      (integration platform, not a CLI family).
- [ ] **Existing external-API code to extract from?** Any worker already talking to QBO/Zoho?
      First wrapper should be *extracted from real usage*, not designed cold.

## Implementation Phases

### Phase 1: Write the CLI standard ✅
Author a repo-neutral `cli/` section in engineering-standards (multi-note, matching the repo's
existing `architecture/`, `testing/` split).
- [x] `cli/command-grammar.md` — `resource action target options`, explicit verbs, help at
      every level, relationships-as-flags-on-edit. Distilled from `cli-design-style.md`.
- [x] `cli/project-structure.md` — the dual lib+CLI 2-in-1 shape; `src/` client vs `bin/` +
      `src/cli/` shell; file-per-action layout; naming conventions.
- [x] `cli/output-formatting.md` — `--json` everywhere, table/field/date helpers, column
      selector, presentation-separated-from-logic (link `principles/scripting/…`).
- [x] `cli/error-handling.md` — `safe()` wrapper, exit codes, stderr vs stdout, debug env var.
- [x] `cli/auth-patterns.md` — api-key example + OAuth2-refresh example; creds file location
      (`~/.fob-<tool>/`), 0600, per-call `credentials` override for multi-tenant workers.
- [x] `cli/subcommand-dispatch.md` — the git-style `fob <tool>` launcher spec.
- [x] `cli/testing.md` — Jest ESM + `captureOutput()`/`safe()` harness, mock the client.
- [x] `cli/README.md` — index + "building a new wrapper" checklist.
- [x] Linked the section into the root `README.md` topic list + structure tree.

### Phase 2: Audit existing CLIs against the standard ✅
Audited all three against the 8-note standard (2026-07-25).

- [x] `cli-fob` — **~80% compliant.** Grammar ✓, output ✓ (minor: list filter diagnostics go to
      stdout, should be stderr — `stations/list.js:10`, `work-records/list.js:12`). Gaps:
      no `safe()` wrapper (30+ handlers duplicate try/catch; debug var is `DEBUG` not `FOB_DEBUG`);
      thin handler tests (only `stations/run.test.js`). **Two deliberate exceptions** (not a
      2-in-1; `.env` not config file) are the *worker-context CLI* variant — now documented in
      the standard, not gaps.
- [x] `cli-fobs` — **compliant + more evolved**, EXCEPT the 2-in-1 gap: `src/index.js` exports
      only `run`; client logic lives in `utils/http.js` + handlers; the worker client is the
      *separate* `lib-worker-statements`. 4-level app-first tree collapses to 3-level on
      extraction. Output/error/auth ✓; handler tests + `captureOutput` missing.
- [x] `lib-email` — **the 2-in-1 shape, weakest CLI.** GAPs: `bin/cli.js` hand-rolled
      `switch`/`parse()` (no yargs, no `src/cli/`, no per-level help); output always-JSON (no
      toggle, no `format.js`); no `safe()`; only `filter` tested. Auth ✓ (protocol pattern).
- [x] Per-CLI decision: **retrofit `lib-email`** (Phase 6) and **fold `cli-fobs`/statements into
      `fob-stm`** (Phase 3). `cli-fob` gets low-effort cleanups only (`safe()`, stderr fix),
      grandfathered as the worker-context variant.

**Standard gaps the audit exposed (fixed in this pass):**
- Added **Pattern C — protocol/connection credentials** to `auth-patterns.md` (lib-email is
  IMAP/SMTP: live `Session`, no `http.js`/`page_context`/`ApiError`).
- Added the **worker-context CLI exception** to `project-structure.md` (cli-fob is CLI-only +
  `.env`, by design).
- Marked the **column selector optional** for small domain CLIs in `output-formatting.md`.

**Path mismatch — RESOLVED (not a conflict).** Both hit `/api/v1/accounts`; they just split
`/api/v1` differently. Workers set `FOB_STATEMENTS_API_URL=…/api/v1` (base includes it) and the
client appends bare `/accounts`; `cli-fobs` uses an origin-only `api_url` and appends
`/api/v1/accounts` in code. **Decision for `fob-stm`: origin-only base URL + `/api/v1/...` in
the code** (cli-fobs convention) — explicit in source, no footgun where an env var must remember
`/api/v1` or bare calls 404. Worker `.env`s drop the `/api/v1` suffix at migration.

### Phase 3: Deprecate `lib-worker-statements` → `fob-stm` ❌
Extract the statements slice into a standalone 2-in-1 `@fob/stm` (client + `fob-stm` CLI).
Concrete plan from the cli-fobs audit:

- [ ] **GATE: resolve the path mismatch first** (bare `/accounts` vs `/api/v1/accounts`) — pick
      the canonical form; the merged client uses one.
- [ ] **Client core** (`src/index.js` exports the functions; `src/client.js` + `src/http.js`):
      merge `lib-worker-statements/statements.js` (function signatures + per-call `credentials`
      override) with `cli-fobs/utils/http.js` (`ApiError`, `apiGetAll` capped pagination,
      `buildAuthedUrl`). Workers `import { getAccounts } from '@fob/stm'`; the CLI calls the same.
- [ ] **CLI files**: copy `cli-fobs/src/cli/statements/*` up one level (drop the `statements/`
      app level) → `accounts/`, `transactions/`, `statements/`, `rules/`, `reports/`,
      `categories/`, `entities/`. Copy `_helpers.js`, `format.js`, `list.js` unchanged.
- [ ] **Collapse the tree**: `fobs statements accounts list` → `fob-stm accounts list`. Drop
      `apps.js` / `apps list`; convert `orgs` meta-commands to `fob-stm config <add|list|use|remove>`.
- [ ] **Credentials**: `~/.fobs/config.yml` (per-org-per-app) → `~/.fob-stm/config.yml`
      (per-org, single tool). Env convention `FOB_STM_API_*`. First-run migration: promote
      `orgs.*.apps.statements` from the old file. Keep the per-call override seam for workers.
- [ ] **Migrate worker consumers** of `@fob/lib-worker-statements` (e.g. `worker-alex`) to `@fob/stm`.
- [ ] **Deprecate** `@fob/lib-worker-statements`: re-export shim → `@fob/stm` for one release, then remove.
- [ ] Remove the statements subtree from `cli-fobs` (aggregator now down to billing/orchestrator).

### Phase 4: Git-style dispatcher ❌
- [ ] Resolve the `fob` naming collision (Open Questions).
- [ ] Implement `fob <tool>` → exec `fob-<tool>` on PATH; bare `fob` lists discovered wrappers.
- [ ] Pass through `--help`/exit codes transparently.

### Phase 5: First external wrapper (proof) ❌
- [ ] Pick `fob-qbo` or `fob-zb` (prefer the one with existing worker usage to extract from).
- [ ] Build to the standard, including the OAuth2-refresh auth pattern.
- [ ] Validate the prototype→worker story end-to-end with a real step.

### Phase 6: Retrofit `lib-email` CLI ❌
- [ ] Replace `bin/cli.js` `parse()`/`switch` with the yargs skeleton + grammar.
- [ ] Add `--json` consistency, per-level help; keep the library exports unchanged.

## Related Files

**Standard (this repo):**
- `docs/wip/cli-standards-and-wrappers.md` — this tracker
- `cli/` — the standard to be written (Phase 1)
- `principles/scripting/separate-presentation-from-logic.md` — existing principle to link

**Reference CLIs (finopsbricks):**
- `cli/cli-fob/docs/cli-design-style.md` — grammar source of truth
- `cli/cli-fob/docs/architecture/sor-cli-convergence.md` — prior decision being partly revisited
- `cli/cli-fobs/docs/architecture/command-signature.md` — app-first tree, short codes
- `cli/cli-fobs/src/utils/` — `safe()`, `format.js`, `http.js`, `config.js`, column selector
- `lib/lib-email/` — dual lib+CLI shape (client good, `bin/cli.js` to be replaced)

**To deprecate / migrate:**
- `lib/lib-worker-statements/src/statements.js` — client to fold into `fob-stm`
- `cli/cli-fobs/src/cli/statements/**` — statements commands to point at the shared client

## Related Notes

- [WIP Files Pattern](/git-workflow/wip-files.md)
- [Separate Presentation from Logic](/principles/scripting/separate-presentation-from-logic.md)
