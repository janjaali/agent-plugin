---
name: code-development
description: General conventions for writing and documenting code and markdown in any language or repo. Covers markdown formatting (uppercase filenames, no hard-wrapped prose), code structure, doc comments, why-comments, ~80-column line width, and no trailing commas. Applies whenever generating, editing, or reviewing source code, configuration, or markdown files.
---

# Code Development

Each item below is a brief summary. Full rationale, decision text, and checklists are in the linked records under `references/`; read the full record before applying it to non-trivial work. These are baseline rules for every language. A more specific, established convention wins, for example a repository's own `.scalafmt.conf` or `.editorconfig`, or a language skill such as `scala-development`. The `CODE-NNNN` numbering is specific to this skill.

## Markdown

- **CODE-0001** (`references/CODE-0001-MARKDOWN-FORMATTING.md`) — Uppercase basenames with a lowercase `.md` extension, and no hard-wrapping of prose. Fenced code blocks in markdown are code, so the code rules below apply to them.

## Code

- **CODE-0002** (`references/CODE-0002-WELL-STRUCTURED-DOCUMENTED-80-COLUMN-CODE.md`) — Single responsibility per unit, idiomatic file organization, no dead code. Doc comments on the public surface, why-comments for anything non-obvious, and code wrapped near 80 columns unless a tool configures otherwise.
- **CODE-0003** (`references/CODE-0003-NO-TRAILING-COMMAS.md`) — No comma after the last element of a comma-separated construct, unless the language or a configured formatter requires one.

## When applying a decision

1. Identify which categories apply to the files being written or changed: markdown, code, or both.
2. Read the full record for any decision that will be applied — the summary above is orientation only.
3. Before finishing, check new and changed content against each applicable record's implementation checklist.
4. If a more specific convention conflicts with a decision, follow the specific one.
