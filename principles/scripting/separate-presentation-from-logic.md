# Separate Presentation From Business Logic

A script that both **computes** something and **emits formatted output** (a report, a worksheet, a status page) has two concerns with different change drivers. Keep them in separate files so editing how the output *looks* cannot break what the script *computes*, and vice versa.

## Why

- **Different rates of change.** Presentation (wording, columns, layout) is tweaked often and casually — sometimes by non-engineers. Business logic is correctness-critical and changes rarely. Coupling them means a formatting tweak risks a correctness bug. (See [One Change Driver](/principles/documentation/one-change-driver.md).)
- **Safe manual edits.** When the layout lives in its own file, someone can restyle the output without reading — or endangering — the computation.
- **Testability.** Pure business logic is easy to unit-test; presentation is easy to snapshot.

## Implementation: template file

Layout lives in a `.md`/`.html` template with holes; the script passes in a prepared view-model plus small formatting helpers.
- Best when the output is *document-shaped* (a markdown/HTML report) and you want to edit it as the thing it produces — markdown styling applies to the template body.
- Name it after the output (`report.md`), not the engine (`report.md.ejs`), so editors render it.
- Markdown output must use **raw** interpolation (EJS `<%- %>`, not `<%= %>` — the latter HTML-escapes `<...>` link syntax and `&` in URLs).
- A small `*-render.js` driver module may load the templates, prepare the view-models, and hold the formatting helpers the templates call — keeping the business script free of layout concerns.
- Examples: worker steps' `report.md` (via `renderLocal`); `ops/bookkeeping/scripts/templates/*.md.ejs` (driven by `reconcile-render.js`).

## Rules

- The business layer **computes**; the presentation layer only **formats**. Prepare a view-model in code and pass it in — never put queries, I/O, or business rules inside a template.
- A `*-render.js` driver module may import low-level helpers from the business module and be imported back (an ES-module cycle) **only if** every cross-reference is used at call time, never at module load. Prefer a shared helper module if the cycle grows.
- When extracting presentation from existing code, prove it's behavior-preserving: regenerate and **diff for byte-identical output** before trusting the refactor.

## Related Notes
- [One Change Driver Per Artifact](/principles/documentation/one-change-driver.md)
- [No Hidden Side Effects](/principles/no-hidden-side-effects.md)
- [Working Docs](https://github.com/finopsbricks/fde-handbook/blob/main/working-docs/README.md) — status/report files are a common thing scripts render
