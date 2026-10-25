# ADR-0004: Prefer `#` Over `//` For Comments In HOCON Configuration Files

- Status: Accepted
- Date: 2026-08-09

## Context

HOCON (used by `application.conf`, `serialization.conf`, and related files under `src/main/resources/`) allows both `#` and `//` comments. In practice, this repository ended up with both markers, often in the same file. Most lines already use `#`, but `//` appears in legacy blocks and then spreads when those blocks are copied.

This inconsistency adds avoidable noise in diffs and gives contributors no clear default when extending configuration blocks.

## Decision

All comments in HOCON configuration files in this repository (`*.conf` under `src/main/resources/` and any future ones) must use `#`, never `//`.

### Required design rules

1. Every comment line, whether on its own line or trailing a value, must start with `#`.
2. `//` must not be introduced as a comment marker in new or edited HOCON files.
3. When editing a block that still uses `//`, convert it to `#` as part of that edit rather than leaving the inconsistency to spread further.
4. This applies to comment syntax only — it has no bearing on HOCON value syntax (e.g. `key: value` vs `key = value`), which is out of scope for this decision.

## Consequences

Positive:

- One comment marker across all config files removes a source of unnecessary diff noise and copy-paste drift.
- `#` is already the dominant style in this repository, so this decision codifies existing practice rather than introducing a new one.

Trade-offs:

- There is no automated formatter or linter enforcing HOCON comment style in this repository today, so compliance relies on code review catching `//` in new or edited config, same as any other unenforced convention here.
- Existing `//` comments outside of actively-edited blocks are not required to be bulk-migrated by this ADR alone; rule 3 above cleans them up opportunistically as those blocks are touched.

## Implementation checklist

1. Convert any remaining `//` comments encountered in HOCON config files to `#`.
2. Review new HOCON config additions for `#`-only comments as part of normal code review.
