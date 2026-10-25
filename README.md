# Agent Plugin

A single, agent-agnostic [Agent Plugin](https://agent-plugins.org/) — an open, vendor-neutral package format for reusable AI agent components. This plugin bundles a set of [Agent Skills](https://agentskills.io/specification), each covering one convention or workflow, so any Agent Plugins-compatible client (GitHub Copilot, or others) can discover and load them consistently.

## Installing

### GitHub Copilot CLI

Copilot CLI is deprecating direct repo/URL/path plugin installs in favor of marketplace-based installs, so register this repository as a marketplace first, then install the plugin from it:

```sh
copilot plugin marketplace add janjaali/agent-plugin
copilot plugin install janjaali-agent-plugin@janjaali-agent-plugin
```

`.github/plugin/marketplace.json` is what makes `marketplace add` work — it's a Copilot CLI-specific discovery file, not part of the Agent Plugins specification (see [Why `marketplace.json`?](#why-marketplacejson) below). Its single entry points at `.` (the repository root), since this repository is one plugin, not a catalog of several.

### Other Agent Plugins-compatible clients

Any client that implements the Agent Plugins specification can load this repository directly as a plugin — point it at the repository root, where `plugin.json` lives.

## Skills

- `skills/code-development` — general conventions for markdown and code (`skills/code-development/references/`): uppercase markdown filenames, no hard-wrapped prose, structure, doc comments, why-comments, ~80-column line widths, and no trailing commas.
- `skills/git-development` — git conventions for agents (`skills/git-development/references/`): the agent commits and pushes only when asked (staging only the files it modified), and commit messages have a period-terminated subject plus a detailed body.
- `skills/scala-development` — Scala conventions (`skills/scala-development/references/`) for test structure, file hygiene, sequence literal style, exhaustive pattern matching, HOCON comment syntax, and validation object return types.
- `skills/spec-driven-development` — a spec-driven development workflow producing SPEC.md and companion documents (DESIGN.md, DOMAIN.md, DATABASE.md, KAFKA.md, CONVENTIONS.md).

## Repository structure

```text
plugin.json                       # Agent Plugins manifest ($schema: https://agent-plugins.org/schemas/1.0.0/plugin.schema.json)
skills/<skill-name>/SKILL.md      # Skill definition (YAML frontmatter: name, description)
skills/<skill-name>/...           # Optional supporting files (for example, references/ with ADRs for code-development, git-development and scala-development)
.github/plugin/marketplace.json   # Copilot CLI marketplace wrapper, not part of the Agent Plugins spec
```

This repository is a single plugin, not a marketplace of plugins — `plugin.json` sits at the repository root and every skill is a subdirectory of `skills/`.

## Adding or changing a skill

1. Add a subdirectory under `skills/<skill-name>/` with a `SKILL.md` file (YAML frontmatter with `name` and `description`, followed by the skill's instructions).
2. Keep the `description` specific enough that a client can determine when the skill applies.
3. Put supporting files in a `references/` subfolder next to the skill's `SKILL.md`, and link each from `SKILL.md` with a one-line summary. Write every reference as an ADR named with a skill-specific prefix and number (for example `CODE-0001-...`, `SCALA-0001-...`, `GIT-0001-...`).
4. Follow the `code-development` skill's conventions in this repository too — for example, avoid hard-wrapping markdown prose.

## Why `marketplace.json`?

The [Agent Plugins specification](https://agent-plugins.org/specification) defines only the portable plugin package itself (`plugin.json`, `skills/`, `mcp.json`); it explicitly leaves distribution, installation, and discovery to each client. A marketplace index format isn't standardized yet — it's an open, unresolved proposal in the spec's own discussion tracker. `.github/plugin/marketplace.json` here exists solely so GitHub Copilot CLI can register and browse this repository as a marketplace ahead of its removal of direct repo installs; it has no bearing on the plugin's portability or spec conformance.
