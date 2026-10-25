---
name: scala-development
description: Scala development conventions for writing or reviewing Scala code and related configuration. Covers test structure, Scalafix/Scalafmt file hygiene, immutable sequence literal style, exhaustive pattern matching, validation object return types, and HOCON comment syntax.
---

# Scala Development

Each item below is a brief summary. Full rationale, decision text, and checklists are in the linked records under `references/`; read the full record before applying it to non-trivial work. The `SCALA-NNNN` numbering is specific to this skill and independent of the repository's own `docs/adr/` records.

## Code and tests

Apply regardless of repo, service, library, or sbt plugin.

- **SCALA-0001** (`references/SCALA-0001-TEST-CONVENTIONS.md`) — Test suites extend `Spec`, use `when`/`should`/`in`, keep reusable helpers in companion objects, use fixed UTC clocks, prepare named random data before DB scope, and separate act/assert clearly.
- **SCALA-0002** (`references/SCALA-0002-SCALAFIX-AND-SCALAFMT-FILE-HYGIENE.md`) — For each changed Scala file, run Scalafix `OrganizeImports` first and Scalafmt second.
- **SCALA-0003** (`references/SCALA-0003-LIST-NIL-OVER-SEQ-ABSTRACTION.md`) — For sequence literals, prefer `List(...)` and `Nil` over `Seq(...)` and `Seq.empty`.
- **SCALA-0005** (`references/SCALA-0005-NO-PARTIAL-FUNCTIONS-EXHAUSTIVE-PATTERN-MATCHING.md`) — Pattern matches over sealed hierarchies must be exhaustive and compile-time enforced; avoid wildcard fallbacks and other partial-function shortcuts.
- **SCALA-0006** (`references/SCALA-0006-VALIDATION-OBJECT-RETURN-TYPE-CONVENTION.md`) — `domain.validation` objects return `Option[Rejection]` when success carries no new value and `Either[Rejection, ValidatedType]` when it produces one; never `Either[Rejection, Unit]`.

## Configuration

- **SCALA-0004** (`references/SCALA-0004-HOCON-CONFIG-COMMENT-SYNTAX.md`) — HOCON comments use `#`, never `//`; convert legacy `//` when touching a block.

## When applying a decision

1. Identify which category(ies) apply to the code being written or reviewed.
2. Read the full record for any decision that will be applied — the summary above is orientation only.
3. If a change conflicts with a decision, flag it explicitly rather than silently working around it or silently complying without comment.
4. Apply only the decisions that match the code being changed; do not force unrelated decisions onto a component.
