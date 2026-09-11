---
name: spec-workflow
user-invocable: true
description: |
   **WORKFLOW SKILL** — Interview the user about specification changes and generate one or more standalone SPEC.md documents (one per spec folder) that serve as a high-level overview of the component or process: contract, behavior, and business intent, captured as user stories and EARS-notation acceptance criteria. Technical architecture, diagrams, and implementation-level detail live in companion documents (DESIGN.md, DOMAIN.md, DATABASE.md, KAFKA.md, CONVENTIONS.md). The agent uses SPEC.md plus companion documents together for planning and code generation.
---

# Spec-Driven Development Skill

## Purpose

Capture each specification as a SPEC.md per spec folder — *what* the component does and *why*, in plain business language — plus purpose-built companion documents for implementation detail. Use both together to plan and generate code.

## Workflow

1. **Interview** — for each spec, clarify: title/scope (in/out), domain terms needing a Glossary entry, feature summary and business value, whether a User Story framing fits, requirements and their acceptance criteria (EARS notation), applicable non-functional requirement categories (performance, security, usability, reliability), technical/business constraints and assumptions, related specs, which companion documents are needed, and whether DESIGN.md is warranted (architecture, diagrams, or implementation-level logic worth documenting).
2. **Draft** — write SPEC.md (see template below) in `/docs/specs/spec-<name>/SPEC.md`. For two or more independently contractable tracks in one folder, write one `UPPER_SNAKE_CASE.md` per track instead, cross-linked via Related Specs. Put implementation detail in companion documents in the same folder, referenced from SPEC.md's Related Docs — never duplicated across documents.
3. **Review** — present drafts, revise until finalized.
4. **Plan/Execute** — use SPEC.md, companion docs, and relevant ADRs (`/docs/adr`) to plan and generate code.

## Specification Template

```markdown
# [Spec Title]

## Glossary  *(optional — domain terms or acronyms a reader needs to understand this spec)*

`**Term** — definition.` one line each.

## Feature Summary

One short paragraph stating what the concept or capability is.

## Business Value

Stakeholder benefit, the business trigger, and the problem this solves — including how it relates to a gating/prerequisite concept in another spec, when relevant. Follow with a bulleted "Business value:" list when there are concrete stakeholder benefits.

## User Story  *(optional — when the spec centers on a user-facing capability rather than a system contract)*

As a [role], I want [capability], so that [benefit].

## Scope

Included/excluded, by business concept — no source-level classes, methods, or fields.

## Requirements

`### REQ-N — Title` per requirement: one short prose sentence stating the capability (no internal class/method names), followed by:

**Acceptance Criteria** — EARS notation, one requirement per line:

- [ ] The `<component>` SHALL `<response>`.  *(ubiquitous — always true)*
- [ ] WHEN `<trigger>`, the `<component>` SHALL `<response>`.  *(event-driven)*
- [ ] WHILE `<state>`, the `<component>` SHALL `<response>`.  *(state-driven)*
- [ ] IF `<trigger>`, THEN the `<component>` SHALL `<response>`.  *(unwanted behavior)*

## Non-Functional Requirements  *(optional — include only the categories that apply)*

`### Performance Requirements` / `### Security Requirements` / `### Usability Requirements` / `### Reliability Requirements` — each a short bulleted list of measurable, observable requirements (e.g. latency budgets, authN/authZ rules, accessibility targets, uptime/error-rate targets). Business language only.

## Constraints and Assumptions

`### Technical Constraints` — limits imposed by existing systems, platforms, or standards the solution must operate within, in business/product terms (no source-level types or signatures — see Formatting & Section Discipline).
`### Business Constraints` — limits imposed by budget, timeline, regulation, or organizational policy.
`### Assumptions` — conditions taken as true without proof; note what happens if an assumption turns out false.

## Open Decisions

Unresolved questions.

## Related Specs

Links to other specs.

## Related Docs

Links to companion documents (including DESIGN.md, when present) and source files.
```

## Formatting & Section Discipline

- Follow the markdown formatting conventions (this plugin's `markdown-formatting` skill) — no manual line-wrapping in markdown files.
- Do not invent sections beyond the template above (no "Implementation Plan", "Agent Responsibilities", "Architecture", "Diagrams", etc.) — planning belongs in the conversation or a task tracker, and technical architecture/diagrams belong in DESIGN.md, not the spec.
- No source-level types or signatures in SPEC.md, ever — that detail belongs in the companion documents below.
- State current behavior as fact, not as a change narrative. Write what the system does, not the history of how it got there — avoid words like "now", "was added", "used to be", "revisit once implemented". When a change lands, edit the affected section(s) to read as if they were always true; don't append a note describing the change.
- Describe behavior, not the code path that produces it — no "the handler consults", "the actor calls", "the request flows through". State the rule or outcome directly (e.g. "a reservation may only draw on points while a period is effective") rather than narrating which component does the consulting.

## Companion Documents

Colocated with SPEC.md in the same folder, referenced (never duplicated) from SPEC.md's Related Docs:

- **DESIGN.md** — technical architecture and implementation considerations, structured per the Design Template below. Unlike SPEC.md, DESIGN.md may reference source-level types, classes, and signatures.
- **DOMAIN.md** — domain-layer contract only: field/type semantics (including opaque `Id`/`Version`-style wrappers) and derived behavior (methods and what they compute). Do not restate business rules (that's SPEC.md's `## Requirements` or `## Constraints and Assumptions`) or persistence enforcement (that's DATABASE.md's Write Constraints) — DOMAIN.md may link to them, not repeat them.
- **DATABASE.md** — persistence layout (tables, columns, keys, indexes) plus a Write Constraints section (invariants enforced at write time, one bullet per rule with what it checks and which rejection it produces) kept separate from Write Actions (the locking/versioning mechanics per method). Skip trivial opaque-type-to-column mapping; call it out only when non-trivial (a discriminator, an enum, a multi-field encoding).
- **KAFKA.md** — consumers, topics, payload fields, consumer-specific configuration.
- **CONVENTIONS.md** — naming conventions, prefixes, and terminology for this spec's domain.

## Design Template

```markdown
# [Component] Design

## Overview

### Design Goal

One short paragraph: what this design achieves and the primary quality attribute (e.g. throughput, consistency, extensibility) it optimizes for.

### Key Design Decisions

Bulleted list of the decisions with the most leverage on the design, each with a one-line rationale. Link to an ADR (`/docs/adr`) instead of repeating its reasoning here.

## Architecture

### System Context

Mermaid `flowchart LR`: this component among the systems/services it talks to — caller(s) → this component → downstream components/systems, edge labels state the purpose of each interaction. Repository/persistence nodes use `[(...)]`.

### High-Level Architecture

Internal structure and layering. `classDiagram` for structure, `stateDiagram-v2` for lifecycle/state transitions, or a `flowchart` of internal modules and how they collaborate — whichever best shows the pattern.

## Components

### Components and Interfaces

One subsection per component: its responsibility, and the interface(s) it exposes or consumes. May reference source-level types and signatures.

### [Operation] Logic  *(optional — one per operation needing branching sub-cases, formulas, or scenario tables)*

`#### Step N — Title` headers; fenced code for expressions; bold **Case X** bullets for branches; a summary table for scenario-driven outcomes.

## Data Models

Structures that cross component boundaries — request/response/event payloads, internal aggregates — not persistence layout (that's DATABASE.md). `classDiagram` or `erDiagram` where a diagram clarifies relationships better than prose.

## Endpoint Design  *(omit for components with no request/response or event API)*

Per endpoint or handler: method/trigger, path/topic, request/response or payload shape, status/result codes. A `sequenceDiagram` for any endpoint with a non-trivial call sequence.

## Security Considerations

AuthN/authZ mechanics and data sensitivity/handling — how the design satisfies SPEC.md's `## Non-Functional Requirements` → Security Requirements.

## Error Handling

Failure modes and how each is surfaced (exceptions, error codes, retries, compensating actions). A failure → detection → response table reads well here.

## Performance Considerations

How the design satisfies SPEC.md's `## Non-Functional Requirements` → Performance Requirements — bottlenecks considered, caching, batching, complexity of hot paths.

## Testing Strategy

Test levels this design requires (unit/integration/contract/e2e); what's already covered by SPEC.md's Acceptance Criteria vs. what's design-specific (concurrency, failure injection, load); fixtures/tooling implications.

## Related Docs

Links to SPEC.md and other companion documents.
```

Do not invent sections beyond this template. Never restate SPEC.md content (requirements, business value, acceptance criteria) — link to it instead.

## Example Prompts

- "Start a new spec-driven development session."
- "Add a new specification for the user registration flow."

## Related Customizations

- `adr-from-code` *(planned — not yet available)*
