# ADR-0003: Prefer `List`/`Nil` Over `Seq`/`Seq.empty` For Sequence Literals

- Status: Proposed
- Date: 2026-08-09

## Context

The repository has lacked a single rule for sequence literal construction. Existing code mixes `Seq(...)`, `List(...)`, `Seq.empty`, `Nil`, and `List.empty` for the same concrete use case: in-memory immutable ordered collections. Usage already leans toward concrete constructors, but not consistently.

`Seq` is an abstract, non-sealed supertype (`scala.collection.immutable.Seq`) that could in principle be backed by `List`, `Vector`, `ArraySeq`, or another implementation. Constructing a literal with `Seq(...)` still allocates a concrete implementation under the hood (the standard library's default `Seq` factory produces a `List`) but exposes it under the wider, less specific `Seq` type, hiding the actual structure and its performance characteristics (`List` is O(1) prepend / O(n) append and pattern-matches naturally via `::`/`Nil`; other `Seq` implementations don't share those guarantees). A recent addition introduced exactly this case — `Seq(rejection)` — where nothing about the call site required the abstract `Seq` type; `List(rejection)` would have been equally correct and more explicit.

Without a documented convention, new code keeps mixing `Seq` and `List` in equivalent situations, making behavior and intent less obvious at a glance.

## Decision

When code needs to construct a concrete, in-memory immutable sequence literal, it must use the `List` constructor and its associated forms, not the `Seq` abstraction:

- Use `List(a, b, c)`, not `Seq(a, b, c)`.
- Use `Nil`, not `Seq.empty`, for an empty concrete sequence literal.
- Use `List.empty[T]` only where type inference needs the explicit type parameter and bare `Nil` would not provide it.

### Required design rules

1. New code constructing a sequence literal (non-empty or empty) must use `List(...)` / `Nil` / `List.empty[T]`, not `Seq(...)` / `Seq.empty[T]`.
2. This rule governs literal construction only; it does not require changing a method parameter's or return type's declared type from `Seq[T]` to `List[T]` — a function may accept/return `Seq[T]` while constructing its value with `List(...)`, since `List <: Seq`.
3. Pattern matching against a value whose declared/static type is already `Seq[T]` (e.g. a method parameter or an `Either`'s `Left` type) may keep using the `Seq(...)` extractor pattern for that match, since asserting a more specific `List(...)` extractor there would silently assume a runtime type the static type doesn't promise. Prefer `List`-specific extractors or `::`/`Nil` deconstruction only where the value being matched was itself constructed as a `List` in the same scope.
4. Existing `Seq(...)`/`Seq.empty` call sites are not required to be migrated proactively; apply this rule to new and touched code. Migrate an existing call site only when it is otherwise being modified in the same change.

## Consequences

Positive:

- A value's actual runtime shape is visible from the constructor used at the call site, without needing to check documentation or trace allocation.
- Consistent with the majority of existing call sites in the repository (`List(...)`/`Nil` already outnumber `Seq(...)`/`Seq.empty`).
- Removes a small but recurring bikeshed: contributors no longer have to choose between `Seq` and `List` for the same simple case.

Trade-offs:

- Contributors coming from codebases that default to `Seq` for flexibility need to unlearn that habit here.
- Function signatures may still say `Seq[T]` (for caller flexibility) while the body constructs a `List`, which can look inconsistent at a glance; rule 2 exists to make this explicit and expected.
- The repository is not fully migrated to this convention yet; some `Seq(...)`/`Seq.empty` call sites predate this ADR and will coexist with new `List`-based code until touched.

## Implementation checklist

1. When writing a new sequence literal, use `List(...)` instead of `Seq(...)`.
2. When writing a new empty sequence literal, use `Nil` instead of `Seq.empty`; use `List.empty[T]` only if the type parameter cannot otherwise be inferred.
3. Leave a method's or field's declared `Seq[T]` type alone unless the change is already touching that signature for another reason.
4. When modifying a line that already contains `Seq(...)`/`Seq.empty` as a literal construction, migrate it to `List(...)`/`Nil` as part of that change.
5. Do not perform a repository-wide mechanical rewrite of existing `Seq(...)`/`Seq.empty` call sites solely to satisfy this ADR.
