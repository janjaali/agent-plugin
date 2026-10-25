# CODE-0003: No Trailing Commas In Code

- Status: Approved
- Date: 2026-10-25

## Context

Many languages and formatters accept, or even encourage, a trailing comma after the last element of a multi-line list, object, argument list, or similar construct. Generated and hand-written code across organization repositories mixes both styles, so equivalent constructs look different from file to file and diffs carry noise from style changes rather than from real changes.

A trailing comma also hides an easy-to-miss signal: when the last element has no comma, the end of the construct is visible at a glance, and the same visual rule holds in every language, including those where a trailing comma is a syntax error (for example JSON) or changes meaning (for example a single-element tuple in Python).

This ADR is language-agnostic. It governs code, including fenced code blocks in markdown and configuration written in languages such as HCL, YAML flow style, and JSON.

## Decision

Code must not use trailing commas. The last element of a list, object, map, argument or parameter list, enum, or any other comma-separated construct is written without a comma after it, even when the construct spans several lines.

### Exceptions

1. **Syntax requires it.** Where the language makes a trailing comma mandatory, such as a multi-line composite literal or argument list in Go, or a single-element tuple in Python, use it.
2. **A configured tool enforces the opposite.** If a repository already configures a formatter or linter that requires trailing commas (for example a Prettier `trailingComma` setting or a rustfmt default), that configuration governs and must not be fought; this ADR is a default for when nothing is configured, not an override of project standards.

## Consequences

Positive:

- Equivalent constructs look the same across languages and repositories.
- The end of a multi-line construct is visible by the missing comma, with no ambiguity about whether another element was forgotten.

Trade-offs:

- Adding an element to the end of a list changes two lines (the previous last line gains a comma) instead of one, which makes diffs slightly noisier.
- Exceptions 1 and 2 mean the rule still requires judgment per language and repository.

## Implementation checklist

1. Before finishing a code-writing task, scan new and changed lists, objects, argument lists, and similar constructs for a comma after the last element and remove it.
2. Check whether the language requires a trailing comma or the repository configures a formatter or linter that enforces one, and follow that instead.
3. Do not reformat unrelated existing code solely to remove trailing commas.
