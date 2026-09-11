# ADR-0005: No Partial Functions — Pattern Matches Over Sealed Hierarchies Must Be Exhaustive

- Status: Proposed
- Date: 2026-08-09

## Context

A partial function handles only part of its declared input domain; unmatched inputs fail at runtime (for example with `MatchError`) instead of producing a value. In Scala, the most common source is a non-exhaustive match over a sealed hierarchy: everything works until a new subtype is introduced and reaches a match site that was never updated.

Sealed hierarchies are valuable precisely because the compiler can check exhaustive handling. A catch-all `case _ =>` on such matches, or similar shortcuts like unchecked `.get`/`.head`/`asInstanceOf`, discards that guarantee and hides missing branches until runtime.

The intended behavior is the opposite: adding a new subtype should surface every impacted match as a compile failure, so required updates are explicit and immediate.

## Decision

Pattern matches over a closed (sealed) hierarchy must list every subtype explicitly, and the build must be configured so that a non-exhaustive match is a compile-time error, not merely a warning that can go unnoticed.

### Required design rules

1. Any domain concept that code pattern-matches over is modeled as a `sealed trait` or `sealed abstract class` with `case class`/`case object` children, so the compiler has a closed set of subtypes to check exhaustiveness against. Do not pattern-match over an open (non-sealed) type when a closed one would do.
2. A pattern match over a sealed hierarchy enumerates every subtype by name. Do not terminate such a match with a wildcard `case _ =>` (or `case _: SomeSupertype =>`) to cover the remaining or "default" cases.
3. A match only needs to name the immediate subtypes of the type it matches over, not every leaf of the whole hierarchy. If one of those immediate subtypes is itself a `sealed trait`/`sealed abstract class` with further children, naming that intermediate subtype as its own explicit branch is enough — it does not need to be exploded into its children inline. When code later needs to distinguish among that intermediate type's own children, it does so with a separate, nested match against that intermediate type, which the compiler checks for exhaustiveness independently, on its own terms. This is not an exception to rule 2: what rule 2 forbids is a branch standing in for "everything not yet named" (`case _ =>` or `case _: SomeSupertype =>`); an intermediate sealed type named as one specific, deliberate branch of the type actually being matched is a real subtype, not a wildcard wearing a type annotation.
4. If several subtypes genuinely share identical handling, list them explicitly in one branch (`case A | B | C =>`), rather than reaching for `_`. A newly added subtype must then fail to compile until it's added to that list or given its own branch — silently joining the group is not acceptable.
5. Do not use an `if` guard on a case pattern to partition a single constructor's values, and do not treat a guarded set of cases as exhausting that constructor. The compiler cannot evaluate an arbitrary boolean guard expression at compile time, so it never credits a guarded case with covering its constructor for exhaustivity purposes — even if a set of guards is logically complete (e.g. `n > 0` and `n <= 0` together cover every `Int`), the checker still reports the match as possibly non-exhaustive, which pushes people toward adding a `case _ =>` (or a guarded case that just throws) to silence the warning, reintroducing the exact wildcard hole rule 2 forbids. If a matched constructor needs different handling depending on a value inside it, match that constructor without a guard and branch with an ordinary `if`/`else` (or a nested match) inside the case body instead — the pattern-level exhaustiveness check then stays intact, and the conditional logic becomes ordinary total code.
6. The build enables exhaustivity checking as a hard compile error, not a warning: add `-Wconf:cat=other-match-analysis:error` to `scalacOptions` (or fold match-exhaustivity into a project-wide `-Xfatal-warnings` if the build already treats all warnings as errors). Do not add a blanket `-nowarn` or a `-Wconf` rule that silences the `other-match-analysis` category.
7. Do not use `Option#get`, `Try#get`, `Either#right.get` / `.left.get`, `List#head`/`.tail`, unchecked `Map` indexing, or `asInstanceOf` as a substitute for a match or a total combinator (`fold`, `map`/`getOrElse`, `collect`, etc.) — each of these is a partial function hiding as a total one, and none of them give the compiler anything to check.
8. Where a framework API forces a genuinely partial function (e.g. an actor's `Receive`, which is `PartialFunction[Any, Unit]`), the partiality of the outer function is an unavoidable consequence of the framework's signature, not an exemption from rule 2: pattern matches on sealed message hierarchies inside that body must still be exhaustive per rules 1-5.

## Consequences

Positive:

- Adding a new subtype to a sealed hierarchy turns every match site that needs updating into a compile error, so the author is guided to every place that needs to change instead of discovering the gap at runtime.
- Removes an entire class of `MatchError` and unsafe-extraction production incidents that only manifest once a new case actually flows through the previously-unhandled path.

Trade-offs:

- Matches over hierarchies with many subtypes are more verbose than a short `case _ =>` fallback, and shared-behavior branches must be written as explicit `case A | B | C =>` lists that need updating alongside the hierarchy.
- Genuinely open types (e.g. `String`, unmodeled external enums) still need a real fallback branch — this decision only removes the wildcard shortcut for hierarchies the codebase itself controls and could have sealed.
- Value-dependent branching that used to live in a case guard has to move into the case body as an `if`/`else` or nested match, which is a small amount of extra nesting compared to a one-line guarded pattern.

## Implementation checklist

1. New domain concept that will be pattern-matched: model it as a `sealed trait`/`sealed abstract class` with `case class`/`case object` children, not as an open type.
2. New pattern match over a sealed hierarchy: list every immediate subtype by name; group identical-handling subtypes with `case A | B | C =>` instead of `_`.
3. Immediate subtype is itself a sealed hierarchy with further children: name it as its own single branch; only add a nested match against it (with its own exhaustive branches) where the code actually needs to distinguish among its children.
4. Need different handling based on a value inside a matched constructor: match the constructor without an `if` guard, then branch on the value with `if`/`else` or a nested match inside the case body — do not add a guard to the pattern itself.
5. Confirm `scalacOptions` includes `-Wconf:cat=other-match-analysis:error` (or an equivalent project-wide fatal-warnings setting that covers match exhaustivity) so a non-exhaustive match fails the build.
6. Replace any `.get`, `.head`, `.tail`, unchecked indexing, or `asInstanceOf` with a pattern match or a total combinator (`fold`, `map`/`getOrElse`, `collect`).
7. When adding a new subtype to an existing sealed hierarchy, let the build fail on every now-non-exhaustive match site and update each one explicitly rather than adding a wildcard or a guard to silence the error.
