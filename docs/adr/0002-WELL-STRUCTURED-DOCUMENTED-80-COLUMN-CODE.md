# ADR-0002: Generated Code Must Be Well-Structured, Fully Documented, And Wrapped Near 80 Columns

- Status: Proposed
- Date: 2026-08-09

## Context

Generated code across organization repositories varies a lot in structure, documentation quality, and line-width consistency. When no explicit convention is provided, assistants tend to produce minimal-comment output, which can be fine for throwaway examples but is risky for long-lived shared code. Important context such as invariants, workarounds, and non-obvious constraints can remain undocumented, making later maintenance harder for engineers who did not author the original change. Likewise, when line width is left unspecified, generated lines can become arbitrarily long, reducing readability in side-by-side diffs, review tools, and terminal workflows that assume roughly 80 columns.

This ADR is intentionally language-agnostic. It does not replace language- or repo-specific standards (for example, ADRs in `scala-conventions` or a repository's own `.scalafmt.conf` and `.editorconfig`); it defines a baseline where no narrower convention exists, and its qualitative expectations around structure and documentation apply everywhere. It also does not modify ADR-0001. ADR-0001 governs markdown *prose* (paragraphs, list item text, table cell text), which remains unwrapped, while this ADR governs *code*, including fenced code blocks in markdown.

## Decision

All code generated for this organization, regardless of language or repository, must be well-structured, fully documented at its public surface, annotated with *why* comments where rationale is not obvious from the code itself, and wrapped near 80 columns unless strict adherence would be unreasonable.

### Required design rules

1. **Structure.** Each function, method, class, or module should have one clear responsibility. Split a unit when it starts handling multiple concerns or when nesting makes control flow difficult to follow. Organize file contents in the order idiomatic to the language (for example: imports, constants/types, public API, private helpers). Do not keep dead code, unreachable branches, or commented-out code.
2. **Documentation of the public surface.** Every publicly visible function, method, class, module, and type must include a doc comment in the language-native format (Scaladoc, JSDoc, Python docstring, Rustdoc, etc.). The comment should cover purpose, parameters and return values where not obvious from names/types, plus any error conditions or side effects callers need to understand.
3. **Why-comments, biased toward more rather than fewer.** Inline comments should explain *why* the code exists in its current form: hidden constraints, subtle invariants, bug workarounds, or non-obvious tradeoffs. They should not restate *what* the code does when clear naming already does that. If uncertain whether rationale is clear enough, add the comment. A slightly redundant comment usually costs less than missing rationale during future debugging.
4. **Line width near 80 columns.** Target about 80 columns for code lines. If repository or language tooling already defines a limit (`maxColumn` in `.scalafmt.conf`, `max_line_length` in `.editorconfig`, linter rules, etc.), that configured value takes precedence. This rule provides a fallback baseline, not an override of project standards.
5. **Do not contort code to satisfy width.** Do not split URLs, string literals, import paths, or other effectively unbreakable tokens only to force compliance with the column target. Allow those rare lines to exceed the target instead of damaging readability. Once a wrapping style is chosen in a file, keep it consistent rather than mixing arbitrary long and wrapped lines.

## Consequences

Positive:

- Public APIs keep their contract close to the code, so readers do not need to infer intent from call sites or commit history.
- Why-comments preserve reasoning behind non-obvious decisions, reducing the risk of reintroducing bugs in later edits.
- A shared ~80-column target improves readability in diffs, side-by-side reviews, and terminal-based workflows.

Trade-offs:

- Generated code is more verbose than minimal-comment output, and additional lines must be maintained as logic evolves.
- Favoring "one comment too many" means some comments will later prove unnecessary; that trade-off is accepted to avoid missing critical rationale.
- Rule 5 allows exceptions for unbreakable tokens, so the 80-column target remains a guideline that requires judgment rather than a strict mechanical ceiling.

## Implementation checklist

1. Before finishing a code-writing task, verify that every new or changed public function, method, class, module, and type has a doc comment covering purpose, parameters/return values, and error conditions or side effects.
2. Review changed logic for non-obvious constraints, invariants, or workarounds and add why-comments wherever rationale may not be clear.
3. Check new and changed lines against an 80-column target, or the repository's configured limit where one exists, and rewrap naturally when needed.
4. Prefer a deliberate over-length line over splitting an unbreakable token (URL, string literal, import path) purely to satisfy the column target.
5. Verify structure: single responsibility per unit, idiomatic file organization for the language, and no dead or commented-out code left behind.