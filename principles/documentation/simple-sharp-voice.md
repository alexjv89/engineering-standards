# Simple, Sharp Voice

Plain words, direct sentences, no filler. The default tone for **all** written material — docs, issues, PRs, memos, chat.

## Principle

Two dials, both turned up:

- **Simple** — the smallest, most ordinary word that is still exact. No jargon for its own sake, no clever phrasing, no throat-clearing.
- **Sharp** — say the thing directly. Lead with the point, cut hedges, and let structure (bullets, tables) carry the load instead of prose.

This is the *tone* layer. It sits on top of the structural rules — [Brevity](/principles/documentation/brevity.md) governs length, [Single Idea Per Note](/principles/documentation/single-idea-per-note.md) governs scope, [Meaningful Titles](/principles/documentation/meaningful-titles.md) governs naming. Voice is *how the sentences sound*.

## The Rules

- **Plainest exact word wins.** Prefer "use" over "utilize", "start" over "commence", "about" over "with regard to". Keep a precise technical term when it's the real name; drop it when it's decoration.
- **Specific beats clever.** A concrete noun or example outranks a witty line. If a sentence is memorable but vague, cut it.
- **Lead with the conclusion.** Put the answer first, the reasoning after. Don't build up to the point.
- **No hedging.** Delete "it's important to note", "you might want to consider", "generally speaking". State it.
- **No meta-commentary.** Don't narrate the document ("This note explains…", "In this section we will…"). The reader can see what the note is.
- **Structure over prose.** Three parallel items = a bulleted list or table, not a paragraph with commas.

## Good Example

```markdown
Boolean variables use `is_`, `has_`, or `should_` prefixes.
```

Says exactly what to do. Nothing before it, nothing after it.

## Bad Examples

### Padded and vague
"When working with variables in your codebase, it's generally considered a good practice to leverage naming conventions that can help communicate intent to other developers who may read your code later on."

Same fact, buried under filler and hedges.

### Clever over specific
"Name booleans like you'd name a yes/no question at a party."

Cute, but the reader still doesn't know the rule. Give the prefixes.

### Meta-commentary
"In this section, we will explore the various conventions that we use for naming boolean variables."

The heading already said that. Delete the sentence, keep the rule.

## Why This Matters

Writing is read far more often than it's written. Plain, sharp prose is faster to scan, harder to misread, and travels across the whole team — sales through engineering — without a decoder. Ornate or hedged writing makes the reader do the work the writer skipped.

It also compounds: when every doc, issue, and message shares one voice, the body of written work reads as a single coherent thing rather than a pile of individual styles.

## Two Notes on Scope

- **Applies everywhere written**, not just docs — issue descriptions, PR summaries, decision logs, and chat messages get the same voice.
- **Meta/explanatory docs are the exception.** A note whose *job* is to explain a system (a principle write-up, an onboarding overview) can carry more connective prose. The "no meta-commentary" rule targets reference and content notes, where explanation-of-the-doc is pure overhead.

## Related Notes
- [Brevity](/principles/documentation/brevity.md) - Cut filler words, keep structure
- [Single Idea Per Note](/principles/documentation/single-idea-per-note.md) - One concept per note
- [Meaningful Titles](/principles/documentation/meaningful-titles.md) - Titles that state the idea
- [MECE](/principles/documentation/mece.md) - Non-overlapping, complete coverage
