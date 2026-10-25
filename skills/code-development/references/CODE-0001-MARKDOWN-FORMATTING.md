# CODE-0001: Markdown Formatting — Uppercase Filenames And No Hard-Wrapped Prose

- Status: Proposed
- Date: 2026-08-09

## Context

Markdown is intentionally written as unwrapped prose lines: one logical line per paragraph, with wrapping handled by the editor or renderer. This applies to ADRs, specs, READMEs, and other project documentation. Some repositories instead hard-wrap markdown prose at a fixed width (commonly 80 or 120 columns), inserting manual line breaks as text reaches that boundary.

That hard-wrapping approach introduces two avoidable problems:

- **Inflated diffs.** A small change inside a hard-wrapped paragraph often reflows subsequent lines, so a tiny edit appears as a noisy multi-line diff.
- **No rendering benefit.** Our editors and viewers already soft-wrap long lines. Fixed-column hard wraps only change source formatting and add maintenance overhead.

Markdown filenames are also kept uniform so documents are easy to spot in a file tree. This ADR makes both conventions explicit so contributors can rely on clear rules.

## Decision

Markdown files use an all-uppercase basename, and markdown prose (paragraph text, list item text, and table cell prose) must not be hard-wrapped to a fixed column. Write each paragraph or list item as one logical source line and let the editor or viewer soft-wrap for display.

### Required design rules

1. Use an all-uppercase basename for markdown files and keep the `.md` extension lowercase (for example, `CONTRIBUTING.md`). Keep existing filenames unless a task explicitly includes renaming them.
2. Do not insert manual line breaks inside a paragraph or list item to keep it under a fixed column width.
3. One paragraph equals one line in the markdown source, regardless of rendered length.
4. This applies to prose only, not to content where line breaks are semantically meaningful, such as fenced code blocks, table rows, or Mermaid source.
5. Do not enable an editor or formatter setting that auto-wraps markdown prose at a fixed column on save.

## Consequences

Positive:

- Diffs stay scoped to the sentence actually changed, instead of reflowing an entire paragraph.
- One less formatting rule for contributors to remember, since soft-wrap already handles readability.

Trade-offs:

- Raw source in narrow terminals can look like very long lines until wrapping is applied.
