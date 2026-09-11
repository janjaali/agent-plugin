# ADR-0002: Scalafix And Scalafmt File Hygiene

- Status: Proposed
- Date: 2026-08-09

## Context

This repository already includes explicit Scalafix and Scalafmt setup in the build:

- `project/plugins.sbt` enables `sbt-scalafix` and `sbt-scalafmt`.
- `build.sbt` enables SemanticDB (`semanticdbEnabled := true`) for Scalafix rules.
- `.scalafix.conf` configures `OrganizeImports` with `removeUnused = true`.
- `.scalafmt.conf` defines the formatter version and rewrite rules.

Current practice uses a two-step cleanup flow for changed Scala files:

1. organize imports via Scalafix (`OrganizeImports`), then
2. format the same file via Scalafmt (`scalafmtOnly`).

That process fits the toolchain, but without an ADR it remains informal and easy to skip.

## Decision

All newly created or modified Scala source files in this repository must follow this required sequence before completion:

1. Run Scalafix `OrganizeImports` on each changed file.
2. Run Scalafmt on each changed file after import organization.

### Required design rules

1. Ordering is mandatory: run Scalafix import organization before Scalafmt.
2. File-scoped execution is required for touched files to keep changes targeted.
3. Import cleanup must use the configured repository rule set in `.scalafix.conf`.
4. Formatting must use repository Scalafmt settings from `.scalafmt.conf`.
5. Do not merge Scala file changes that skip either step.

### Command contract

For each changed Scala file `<path>`:

1. `scalafix --files <path> OrganizeImports`
2. `sbt "<module> / scalafmtOnly <path-from-module>"` (module-scoped `scalafmtOnly` command)

## Consequences

Positive:

- import order and unused-import cleanup are applied consistently
- formatting is predictable and aligned with repository style
- diffs become smaller and easier to review across contributors

Trade-offs:

- contributors must run two commands per changed Scala file
- strict sequencing adds a small workflow overhead during local iteration

## Implementation checklist

1. Ensure `sbt-scalafix` and `sbt-scalafmt` plugins remain enabled in build plugins.
2. Keep SemanticDB enabled for Scalafix compatibility in build settings.
3. For every changed Scala file, run `OrganizeImports` first.
4. For every changed Scala file, run `scalafmtOnly` second.
5. Review resulting diffs to confirm only intended changes remain.
