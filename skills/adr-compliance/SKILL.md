---
name: adr-compliance
description: Enforces the active Scala ADR set from this plugin's adr/ directory, covering testing conventions, Scala file hygiene, immutable sequence literal style, HOCON comment syntax, and exhaustive matching rules. Applies when writing or reviewing Scala code and related configuration.
---

# ADR Compliance

Each item below is a brief summary. Full rationale, decision text, and checklists are in the linked files under `adr/`; read the full ADR before applying it to non-trivial work.

## Universal — Testing & Style

Apply regardless of repo, service, library, or sbt plugin.

- **ADR-0001** (`adr/0001-scala-test-conventions.md`) — Test suites extend `Spec`, use `when`/`should`/`in`, keep reusable helpers in companion objects, use fixed UTC clocks, prepare named random data before DB scope, and separate act/assert clearly.
- **ADR-0002** (`adr/0002-scalafix-and-scalafmt-file-hygiene.md`) — For each changed Scala file, run Scalafix `OrganizeImports` first and Scalafmt second.
- **ADR-0003** (`adr/0003-list-nil-over-seq-abstraction.md`) — For sequence literals, prefer `List(...)` and `Nil` over `Seq(...)` and `Seq.empty`.
- **ADR-0005** (`adr/0005-no-partial-functions-exhaustive-pattern-matching.md`) — Pattern matches over sealed hierarchies must be exhaustive and compile-time enforced; avoid wildcard fallbacks and other partial-function shortcuts.

## Configuration

- **ADR-0004** (`adr/0004-hocon-config-comment-syntax.md`) — HOCON comments use `#`, never `//`; convert legacy `//` when touching a block.

## When applying a decision

1. Identify which category(ies) apply to the code being written or reviewed.
2. Read the full ADR file for any decision that will be applied — the summary above is orientation only.
3. If a change conflicts with a decision, flag it explicitly rather than silently working around it or silently complying without comment.
4. Apply only the decisions that match the code being changed; do not force unrelated ADRs onto a component.
