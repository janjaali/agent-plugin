# ADR-0001: Scala Test Conventions

- Status: Proposed
- Date: 2026-08-09

## Context

This codebase already uses a shared ScalaTest base trait, `Spec`, and many existing suites follow a consistent WordSpec layout with predictable helper placement.

Common test patterns are visible across current modules and are worth standardizing for all new and modified tests:

- suites extending `Spec`
- `when`-`should`-`in` style test structure
- reusable test helpers and constants placed in companion objects
- fixed UTC clocks in tests via `Clock.fixed(...)`

This ADR formalizes that style so test changes stay consistent over time.

## Decision

All newly created or modified Scala test suites in this repository must follow the conventions below:

1. Test suites must inherit from the shared `Spec` trait (typically via `with Spec`).
2. Tests must use ScalaTest WordSpec `when`-`should`-`in` structure.
3. Reusable `val`s and helper methods should be moved into the test companion object where possible.
4. If a clock is needed in tests, use a fixed UTC clock based on `Instant.now()`, preferably:
   `private val fixedClock: Clock = Clock.fixed(Instant.now(), ZoneId.of("UTC"))`
5. Use an explicit fixed instant only when deterministic assertions require stable timestamp values. If one is needed alongside a fixed clock, derive it from the clock via `fixedClock.instant()` — never use a hardcoded `Instant.parse(...)` value.
6. If ScalaCheck generators are used as fixture factories in example-based tests, expose companion-object helper methods in `randomXxx()` style, for example:
   `private def randomAccount() = { accountGen.sample.get }`
7. Private `randomXxx()` helper methods in companion objects should be sorted alphabetically by method name.
8. Generate all named random test data (calls to `randomXxx()` helpers and any generator sampling stored in a `val`) before entering database scope (e.g., `withInMemoryJdbcDatabase`). Only `sut` construction and operations that genuinely require `database` or `schema` belong inside the block. Truly inline, unreferenced generator calls that are passed directly as arguments may remain inside the block.
9. Separate the act and assert phases: capture the asserted `sut` call as `val eventualResult = sut.someMethod(...)` without resolving it, then resolve it with `.futureValue` (or `.failed.futureValue`) on a dedicated line immediately before the assertions. When a test involves multiple sequential `sut` `Future` calls, chain them in a single for-comprehension — use `_ <- sut.setupCall(...)` for intermediate calls and `result <- sut.assertedCall(...)` for the final one, then `} yield result` — and call `.futureValue` once on `eventualResult`. Non-`sut` setup futures (e.g. `database.runTransactionally(...)`) remain outside the comprehension and may chain `.futureValue` directly.

### Required design rules

1. Do not introduce new test suites that bypass the shared `Spec` trait unless a technical limitation requires it and is documented in the test.
2. Keep scenario hierarchy readable by nesting `when` and `should` blocks before `in` examples.
3. Keep suite-level state minimal; place reusable generators, clocks, and data builders in companion objects.
4. Use UTC for fixed clocks to avoid timezone-dependent test behavior. Derive any required fixed instant from the clock via `fixedClock.instant()`; do not hardcode `Instant.parse(...)` values.
5. When using generators in non-property tests, keep generator sampling behind named companion-object helper methods to preserve readability and consistency.
6. Keep private `randomXxx()` companion-object helper methods alphabetically ordered to make test fixtures easier to scan.
7. Generate all named random test data before entering database scope (e.g., `withInMemoryJdbcDatabase`). Only `sut` construction and operations that genuinely require `database` or `schema` belong inside the block. Truly inline, unreferenced generator calls passed directly as arguments may remain inside the block.
8. Separate act from assert: capture the asserted `sut` call as `val eventualResult = sut.someMethod(...)` without resolving it, then resolve it with `.futureValue` (or `.failed.futureValue`) on a dedicated line immediately before the assertions. When a test involves multiple sequential `sut` `Future` calls, chain them in a single for-comprehension — use `_ <- sut.setupCall(...)` for intermediate calls and `result <- sut.assertedCall(...)` for the final one — and call `.futureValue` once on `eventualResult`. Non-`sut` setup futures remain outside the comprehension and may chain `.futureValue` directly.

## Consequences

Positive:

- test structure remains uniform across modules
- new tests are easier to read and review
- helper extraction reduces duplication inside test classes
- fixed UTC clocks reduce timezone-dependent flakiness

Trade-offs:

- stricter conventions reduce stylistic flexibility in individual test files
- moving helpers to companion objects may add extra navigation between class and object

## Implementation checklist

1. Verify each newly added/modified Scala test suite extends `Spec`.
2. Verify test descriptions are structured with `when`-`should`-`in`.
3. Move reusable values and helper methods from class scope to the companion object when they do not require instance state.
4. Replace non-deterministic clock usage with `Clock.fixed(Instant.now(), ZoneId.of("UTC"))` where a clock dependency exists.
5. Use explicit fixed instants only in tests that assert exact time values. Where needed, derive the instant from the fixed clock via `fixedClock.instant()`. Remove any hardcoded `Instant.parse(...)` values.
6. If generators are used in non-property tests, confirm generator sampling is done via companion-object `randomXxx()` helper methods instead of inline `.sample.get` in test bodies.
7. Confirm private `randomXxx()` helper methods are sorted alphabetically.
8. Confirm all named random `val`s are declared before `withInMemoryJdbcDatabase` (or equivalent database scope). Only `sut` creation and database/schema-dependent operations should remain inside the block.
9. Confirm the asserted `sut` call is captured as `val eventualResult = sut.someMethod(...)` and resolved with `.futureValue` on a separate line before the assertions. When a test has multiple sequential `sut` `Future` calls, confirm they are combined into a single for-comprehension with `.futureValue` called once on `eventualResult`. Non-`sut` setup futures remain outside the comprehension.