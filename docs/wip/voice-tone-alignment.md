# Voice & Tone Alignment Pass

## Status: COMPLETE

Align existing documentation with [Simple, Sharp Voice](/principles/documentation/simple-sharp-voice.md). A parallel audit of ~230 notes found ~40 hits, almost all low-severity. This pass fixes the **high-confidence set**: hedge words that soften real rules, meta-commentary opener lines, and padded rationales. Borderline explanatory-prose calls are left alone.

---

## Problem Statement

The corpus already reads in the standard's voice, but a thin layer of misses remains:

1. **Hedge words** (`may`, `typically`, `consider`, `might`, filler qualifiers like "proper"/"significant") softening rules the docs otherwise state directly.
2. **Meta-commentary openers** ("This note covers…", "Here's what we found:") — the standard names this one explicitly.
3. **Padded rationales** — generic "for consistency and maintainability" benefit-strings and decoration ("robust… mechanisms").

## Proposed Solution

Edit each flagged line to the concrete rewrite from the audit. Skip low-severity / judgment-call items in explanatory docs. Commit per phase.

## Implementation Phases

### Phase 1: AI-IMPLEMENTATION-RECOMMENDATIONS.md ✅
- [x] L3 — "Based on analysis…this document provides consolidated patterns…" → drop the doc-narration
- [x] L17 — "Areas for standardization and improvement are documented below." → delete
- [x] L483 — heading "Future Improvements to Consider" → "Future Improvements"
- [x] L550 — "The elevate-ai implementation provides an excellent reference… The recommendations above…" → tighten to a direct instruction

### Phase 2: architecture/ai ✅
- [x] `ai-tool-server-only.md:7` — comma-prose list of 3 sensitive resources → bullets
- [x] `ai-sdk-error-recovery.md:1,3` — "robust error handling and recovery mechanisms" → drop decoration
- [x] `ai-sdk-chat-persistence.md:3` — "chat history and message persistence using localStorage" → de-duplicate
- [x] `ai-sdk-dynamic-model-selection.md:3` — "different AI models…based on their needs" → drop filler

### Phase 3: architecture (rest) ✅
- [x] `jsdoc/typecheck/jsdoc-disable-tscheck.md:3` — "legitimately cannot use" + comma-prose reasons
- [x] `jsdoc/typecheck/jsdoc-disable-tscheck.md:64` — "These typically work" → "These work"
- [x] `jsdoc/types/jsdoc-discriminated-unions.md:111` — "may not narrow correctly" → "fails to narrow"
- [x] `jsdoc/types/jsdoc-discriminated-unions.md:76,97,111` — drop "proper" before "narrowing"
- [x] `jsdoc/typecheck/jsdoc-typecheck-tools.md:34` — "may use a different version" → "can use a different version, which changes results"
- [~] `jsdoc/frameworks/jsdoc-shadcn-component-props.md:16` — SKIPPED: the line states the specific pattern (inline type ∩ `React.HTMLAttributes`), not doc-narration; it's a valid specific lead-in, not padding
- [x] `mdx-library-choice.md:23` — "Here's what we found:" → delete
- [x] `mdx-library-choice.md:1,4` — "choose the right library based on your needs" → drop tail
- [x] `database/column-naming.md:40` — "**Recommendation**: Use…" → direct instruction
- [x] `arrow-vs-declaration.md:66` — "Both work, but arrow functions common…" → fix broken hedge
- [x] `nextjs-generate-metadata.md:73` — heading "When You Might Want Policy Check" → "Add a policy check only when:"
- [x] `functional/array-methods-functional.md:202` — "consider combining operations" → direct
- [x] `functional/immutability-patterns.md:190` — "consider libraries" → "use a library like Immer"
- [x] `state/url-filter-state.md:160` — "uncomfortably large" → "too large"

### Phase 4: storybook ✅
- [x] `argtypes/basics.md:3` — "This note covers…" → delete
- [x] `argtypes/basics.md:11` — "significant interaction or configuration" → drop "significant"
- [x] `argtypes/control-types.md:1,3` — "Complete reference catalog of all…" → tighten
- [x] `decorators/patterns.md:3` — "This note covers common decorator patterns." → delete
- [x] `documentation/custom-description-banner.md:169` — "If you want to modify…" → "To modify…"
- [x] `documentation/mdx-component-docs.md:70` — "The simplest way to use DatePicker:" → "Basic usage:"
- [x] `documentation/mdx-component-docs.md:82` — "You can restrict…" → "Restrict…"
- [x] `organization/file-structure.md:54` — generic "readability and maintainability" rationale → concrete
- [x] `organization/title-convention.md:20` — generic "for consistency and easy navigation" rationale → concrete

### Phase 5: testing ✅
- [x] `jest-esm-setup.md:2` — drop subtitle narrating the doc
- [x] `parametric-testing.md:38` — "Test multiple scenarios with nested test cases:" → delete/tighten
- [x] `test-utilities.md:4` — "Organize test utilities in a dedicated file:" → drop redundancy with heading

### Phase 6: git-workflow / config ✅
- [x] `git-workflow/config-object-anti-pattern.md:26` — "doesn't provide any actual abstraction" → "provides no abstraction"

## Related Files

- `principles/documentation/simple-sharp-voice.md` — the standard being enforced
- `principles/documentation/brevity.md` — length rules (adjacent)

## Notes

- Scope exception honored: principle write-ups (`principles/`) and README-style explanatory docs were audited under the standard's meta-doc exception and came back clean — no edits.
- Deliberately skipped (borderline / explanatory-doc judgment calls): `naming/files-crud-pages.md:59`, `AI-IMPLEMENTATION-RECOMMENDATIONS.md:252` ("SHOULD include"), `deployment/deploy-pipeline.md:27` ("allows"), `principles/javascript-with-jsdoc.md` redundancy, `storybook/mocking/data.md:84`.
