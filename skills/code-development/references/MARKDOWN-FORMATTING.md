# Markdown Formatting

Per ADR-0001 (`docs/adr/0001-NO-HARD-WRAPPING-IN-MARKDOWN-FILES.md` in this repo), do not hard-wrap markdown prose at a fixed column.

## Rule

- Use an all-uppercase basename for Markdown files and keep the `.md` extension lowercase (for example, `CONTRIBUTING.md`). Keep existing filenames unless a task explicitly includes renaming them.
- Write each paragraph or list item as a single logical line in the source file. Let the editor or viewer soft-wrap it for display.
- Applies to prose only — paragraphs, list items, table cell text.
- Does not apply to fenced code blocks, table row structure, or Mermaid diagram source, where line breaks are semantically meaningful.
- Do not enable an editor or formatter setting that auto-wraps markdown prose at a fixed column on save.

Applies to all markdown content in every repo across the organization: SPEC.md files, ADRs, READMEs, and other documentation.
