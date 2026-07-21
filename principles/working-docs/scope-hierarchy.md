# Scope Hierarchy: Place Each Item at Its Lowest Containing Scope

Working docs live at nested scopes (coarse → fine, e.g. org → account → period). The same *type* can exist at several scopes — an org status and an account status are both "status," but about different-sized things.

## The placement rule

**Put each fact or item at the lowest scope that fully contains it. Higher scopes link or aggregate — they never repeat the detail.**

- A fact about one account → account scope only. The org file *links* to it.
- An action spanning three accounts → org scope (no single account contains it). Don't scatter it into each account's to-do.

## Why: repetition = drift

The same detail written in two places is two places to update. One will be forgotten, and the two silently disagree. A link cannot drift; a copy always can. This is [MECE](/principles/documentation/mece.md) applied across scopes.

## What a higher scope holds

A higher-scope file is a **roster / index**: one line per child (headline + count + link), plus any items that genuinely belong only at that scope. It must stand alone as an entry point — you start there and drill down — *without* duplicating what the child files say. Achieve that by [generating it from the children](/principles/working-docs/generated-rollups.md), not by hand-copying.

## Related Notes
- [Working Docs](/principles/working-docs/README.md)
- [MECE](/principles/documentation/mece.md)
- [Generate Higher Scopes From Lower](/principles/working-docs/generated-rollups.md)
- [Hub and Spoke Doc Placement](/principles/documentation/hub-spoke-doc-placement.md)
