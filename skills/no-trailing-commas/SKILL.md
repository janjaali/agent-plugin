---
name: no-trailing-commas
description: Forbids trailing commas in generated or edited code org-wide. Applies to all languages and repos, including configuration and fenced code blocks, unless the language or a configured formatter requires one.
---

# No Trailing Commas

Per ADR-0003 (`docs/adr/0003-NO-TRAILING-COMMAS.md` in this repo), do not put a comma after the last element of a list, object, map, argument or parameter list, enum, or any other comma-separated construct, even when it spans several lines.

## Rule

- Write the last element of a multi-line construct without a trailing comma.
- Applies to all code, including configuration (HCL, JSON, YAML flow style) and fenced code blocks in markdown.
- **Exception: syntax requires it.** Where the language makes it mandatory (a multi-line composite literal or argument list in Go, a single-element tuple in Python), use it.
- **Exception: a configured tool enforces it.** If the repository configures a formatter or linter that requires trailing commas (for example Prettier `trailingComma` or a rustfmt default), follow that configuration instead.
- Do not reformat unrelated existing code solely to remove trailing commas.

## When applying this skill

1. Before finishing a code-writing task, scan new and changed lists, objects, and argument lists for a comma after the last element and remove it.
2. Check whether the language or a configured tool requires one before removing it.
