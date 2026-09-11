---
name: markdown-formatting
description: Enforces markdown formatting conventions org-wide — no manual line-wrapping in prose. Applies to all markdown files (SPEC.md, ADRs, READMEs, docs) regardless of repo or language.
---

# Markdown Formatting

Per ADR-0001 (`docs/adr/0001-no-hard-wrapping-in-markdown-files.md` in this repo), do not hard-wrap markdown prose at a fixed column.

## Rule

- Write each paragraph or list item as a single logical line in the source file. Let the editor or viewer soft-wrap it for display.
- Applies to prose only — paragraphs, list items, table cell text.
- Does not apply to fenced code blocks, table row structure, or Mermaid diagram source, where line breaks are semantically meaningful.
- Do not enable an editor or formatter setting that auto-wraps markdown prose at a fixed column on save.

Applies to all markdown content in every repo across the organization: SPEC.md files, ADRs, READMEs, and other documentation.
