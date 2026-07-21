# Instances Carry No System-Documentation

A working-doc file contains only its own content — never an explanation of the working-doc *system*. No "generated, don't edit" banner, no "rolled up from X; for history see Y" preamble, no description of what the file is or how it's maintained.

## Why: no audience

That boilerplate serves no one:
- The **reader** (auditor / operator) wants the data, not meta-commentary.
- The **generator / agent** knows the rules from its own instructions, not from the file.
- A stray **human editor** should learn "which files are generated" from the naming convention and this standard — written **once** — not from a banner stamped on hundreds of files.

It is the same failure as a status section inside a knowledge note (see [Knowledge vs. Report](/principles/documentation/knowledge-vs-reports.md)): documentation-of-the-system leaking into instances-of-the-system, where it is repeated N times and drifts.

## What a file may contain

1. Its own **data / content**.
2. **Navigation** for its reader (links to related scopes or types).
3. **Legends** that help interpret its own data (e.g. "Months clean = months proven to tie to the app").

Anything describing what the file *is*, how it is produced, or where other content lives → this standard, not the file.

## Related Notes
- [Working Docs](/principles/working-docs/README.md)
- [Brevity](/principles/documentation/brevity.md)
- [Knowledge Base vs. Activity Report](/principles/documentation/knowledge-vs-reports.md)
