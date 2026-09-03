# Discovery Patterns

How to find and navigate atomic notes.

## For LLMs

### 1. Search by Topic
Use directory structure for initial discovery:
```
naming/          # Naming conventions
testing/         # Testing practices
architecture/    # Architecture patterns
```
If the folder has a `readme.md`, it's optional orientation — read it if the folder's purpose isn't obvious, skip it otherwise.

### 2. Identify Relevant Notes
List files in directory to see all available notes.

### 3. Load Specific Notes
Fetch only the atomic notes needed for current task. Skip irrelevant notes.

### 4. Follow Links
Navigate through cross-references at end of each note.

## For Humans

### Discovery Methods

**Browse directories**: List files in topic folders (`ls naming/`, `ls testing/`); check for a `readme.md` if the folder's purpose isn't already clear
**Follow links**: Navigate through related notes
**Search**: Use grep or IDE search for keywords

### Common Workflows

**Learning a topic:**
1. List files in directory (`ls naming/`)
2. Read relevant notes based on filenames
3. Follow links to related patterns

**Looking up specific pattern:**
1. Search by keyword or browse directory
2. Read atomic note
3. Follow links if need related info

## Related Notes
- [LLM Benefits](/principles/documentation/llm/llm-benefits.md)
- [Cross-Linking Notes](/principles/documentation/linking/cross-linking-notes.md)
- [Meaningful Titles](/principles/documentation/voice/meaningful-titles.md)
- [Folder README, Not Index](/principles/documentation/structure/folder-readme.md) - optional first stop before browsing, not a required one
