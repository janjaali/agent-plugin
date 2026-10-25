---
name: code-development
description: General conventions for writing and documenting code and markdown in any language or repo. Covers code structure, doc comments, why-comments, ~80-column line width, no trailing commas, and markdown formatting (no hard-wrapped prose, uppercase filenames). Applies whenever generating, editing, or reviewing source code, configuration, or markdown files.
---

# Code Development

Each item below is a brief summary. The full rules are in the linked files under `references/`; read the full file before applying it to non-trivial work. These are baseline rules for every language. A more specific, established convention wins, for example a repository's own `.scalafmt.conf` or `.editorconfig`, or a language skill such as `scala-development`.

## Code

- **Structure and documentation** (`references/STRUCTURE-AND-DOCUMENTATION.md`) — Single responsibility per unit, idiomatic file organization, no dead code. Doc comments on the public surface, why-comments for anything non-obvious, and code wrapped near 80 columns unless a tool configures otherwise.
- **No trailing commas** (`references/NO-TRAILING-COMMAS.md`) — No comma after the last element of a comma-separated construct, unless the language or a configured formatter requires one.

## Markdown

- **Markdown formatting** (`references/MARKDOWN-FORMATTING.md`) — Uppercase basenames with a lowercase `.md` extension, and no hard-wrapping of prose. Fenced code blocks in markdown are code, so the code rules above apply to them, including the 80-column target and no trailing commas.

## When applying

1. Identify which categories apply to the files being written or changed: code, markdown, or both.
2. Read the full reference file for each rule you apply.
3. Before finishing, check new and changed content against each applicable rule.
4. If a more specific convention conflicts with a rule here, follow the specific one.
