---
name: code-structure-and-documentation
description: Enforces structure, documentation, comment, and ~80-column line-width conventions org-wide for generated or edited source code. Applies to all languages and repos, regardless of any more specific language convention.
---

# Code Structure And Documentation

Per ADR-0002 (`docs/adr/0002-well-structured-documented-80-column-code.md` in this repo), generated code must be well-structured, fully documented at its public surface, commented on the *why* wherever it isn't obvious, and wrapped near 80 columns.

This is a baseline that applies everywhere, regardless of language. It does not override a more specific, already-established convention — for example a repository's own `.scalafmt.conf`/`.editorconfig` line-width setting, or a language-specific ADR such as this plugin's `adr-compliance` skill. Where a more specific convention exists, follow it; this skill fills the gap where none does.

## Rules

- **Structure.** Single responsibility per function/method/class/module; break up units that do more than one job or nest too deeply to follow. Order file contents idiomatically for the language (imports, types/constants, public API, private helpers). No dead code, unreachable branches, or commented-out code.
- **Document the public surface.** Every publicly visible function, method, class, module, and type gets a doc comment in the language's native form (Scaladoc, JSDoc, Python docstring, Rustdoc, etc.) covering purpose, parameters/return where not self-evident, and any error conditions or side effects a caller needs to know.
- **Why-comments, biased toward more rather than fewer.** Inline comments explain *why*, not *what* — well-named identifiers already say what. When in doubt whether a why-comment is needed, add it: one comment too many costs a moment of skimming; one missing costs a debugging session.
- **~80 columns.** Target 80 columns for code lines. If the repo or toolchain already configures a limit (`.scalafmt.conf` `maxColumn`, `.editorconfig` `max_line_length`, a linter rule), that governs instead — this is a default for when nothing is configured, not an override.
- **Don't contort code to hit the limit.** Don't break a URL, string literal, import path, or other essentially unbreakable token across lines just to satisfy the column count — let that one line run long instead. Once a wrapping style is adopted for a file, apply it consistently rather than wrapping some lines and leaving others long for no reason.

## When applying this skill

1. Before finishing a code-writing task, check every new/changed public function, method, class, module, and type has a doc comment.
2. Scan changed logic for non-obvious constraints, invariants, or workarounds and add a why-comment wherever one is plausibly needed.
3. Check new/changed lines against 80 columns, or the repo's configured limit if stricter or looser; rewrap naturally where over.
4. Leave a line over-length rather than mangling an unbreakable token to force it under the limit.
5. Check structure: single responsibility per unit, idiomatic file organization, no dead or commented-out code left behind.
