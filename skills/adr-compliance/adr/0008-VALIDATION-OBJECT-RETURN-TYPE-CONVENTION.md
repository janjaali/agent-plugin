# ADR-0008: `domain.validation` Objects Return `Option[Rejection]` Unless They Produce a Validated Value

- Status: Proposed
- Date: 2026-08-09

## Context

The `domain.validation` package holds standalone `object`s with a single `apply` method that check one business rule
and report whether it was violated. Two kinds exist so far:

- One kind, given a raw pair of values, either finds they don't form a valid range, or they do — and if they
  do, a richer domain value is constructed from them. There is a real, richer value to hand back on success.
- Another kind checks one raw input value against a point in time and has nothing more to report on success —
  the input is already fully known to the caller.

The latter kind was originally written as `Either[Rejection, Unit]`, matching the first kind's shape for
consistency. But `Unit` on the `Right` side carries no information — the caller already has everything it needs
(there is no validated value being produced), so the `Either` only exists to smuggle out a rejection. `Option[Rejection]`
says the same thing more directly: the check either found a problem (`Some(rejection)`) or didn't (`None`). Using
`Either[Rejection, Unit]` for this case forces every call site to reach for `.left.toOption` just to get back to an
`Option`, which is exactly what the validation should have returned in the first place.

## Decision

A `domain.validation` object's `apply` method's return type is chosen by what it produces on success:

- If the validation only checks a rule and produces no new value beyond confirming the input was fine, it returns
  `Option[SpecificRejection]` — `None` when the input passes, `Some(rejection)` when it doesn't.
- If the validation constructs and returns a genuinely validated result (a different/narrower type than what was
  passed in, e.g. a validated range value from a raw pair of bounds), it returns `Either[SpecificRejection, ValidatedType]`
  — `Right(validatedValue)` on success, `Left(rejection)` on failure.

`Either[Rejection, Unit]` must not be used in this package: if there is nothing but `Unit` to return on success, that
is the signal to use `Option[Rejection]` instead.

### Required design rules

1. A validation object with no validated result to produce (success carries no new information) returns
   `Option[SpecificRejection]`.
2. A validation object that constructs a validated value on success returns `Either[SpecificRejection, ValidatedType]`.
3. Never use `Either[SpecificRejection, Unit]` in this package — that shape always collapses to rule 1's
   `Option[SpecificRejection]`.
4. Callers collecting rejections from a mix of both shapes normalize `Either`-shaped validations with `.left.toOption`
   before combining them with `Option`-shaped ones (e.g. in a `List(...).flatten`).

## Consequences

Positive:

- The return type alone tells a reader whether the validation produces a new value or merely gates on a condition —
  no need to open the method body to find out.
- Removes the need for `.left.toOption` boilerplate at call sites for the common no-new-value case.

Trade-offs:

- A package with a mix of `Option`-returning and `Either`-returning validations means callers collecting rejections
  across several validations must normalize both shapes (rule 4) rather than folding over one uniform type.

## Implementation checklist

1. New validation object, success carries no new value: return type is `Option[SpecificRejection]`; failure is
   `Some(rejection)`, success is `None`.
2. New validation object, success constructs a validated value: return type is
   `Either[SpecificRejection, ValidatedType]`; failure is `Left(rejection)`, success is `Right(validatedValue)`.
3. Do not introduce `Either[SpecificRejection, Unit]` in this package.
4. When collecting rejections from several validations of mixed shape, convert `Either`s with `.left.toOption` and
   combine with the `Option`-returning ones directly (e.g. in a `List(rangeValidation.left.toOption, endsInPastValidation, startsInPastValidation).flatten`).
