# Agent Plugin

A single, agent-agnostic [Agent Plugin](https://agent-plugins.org/) — an open, vendor-neutral package format for reusable AI agent components. This plugin bundles a set of [Agent Skills](https://agentskills.io/specification), each covering one convention or workflow, so any Agent Plugins-compatible client (GitHub Copilot, or others) can discover and load them consistently.

## Skills

- `skills/code-structure-and-documentation` — well-structured, fully documented code with why-comments and ~80-column line widths.
- `skills/markdown-formatting` — no manual line-wrapping in markdown prose.
- `skills/adr-compliance` — enforces this plugin's Scala ADRs (`skills/adr-compliance/adr/`) for test structure, file hygiene, sequence literal style, exhaustive pattern matching, HOCON comment syntax, and validation object return types.
- `skills/spec-workflow` — a spec-driven development workflow producing SPEC.md and companion documents (DESIGN.md, DOMAIN.md, DATABASE.md, KAFKA.md, CONVENTIONS.md).

## Repository structure

```text
plugin.json                  # Agent Plugins manifest ($schema: https://agent-plugins.org/schemas/1.0.0/plugin.schema.json)
skills/<skill-name>/SKILL.md # Skill definition (YAML frontmatter: name, description)
skills/<skill-name>/...      # Optional supporting files (for example, adr/ for adr-compliance)
docs/adr/                    # ADRs that govern this repository itself
```

This repository is a single plugin, not a marketplace of plugins — `plugin.json` sits at the repository root and every skill is a subdirectory of `skills/`.

## Adding or changing a skill

1. Add a subdirectory under `skills/<skill-name>/` with a `SKILL.md` file (YAML frontmatter with `name` and `description`, followed by the skill's instructions).
2. Keep the `description` specific enough that a client can determine when the skill applies.
3. Place any supporting files (references, scripts, ADRs) alongside the skill's `SKILL.md`.
4. Follow this repository's own ADRs in `docs/adr/` — for example, avoid hard-wrapping markdown prose.
