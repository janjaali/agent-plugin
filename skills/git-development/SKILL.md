---
name: git-development
description: Git conventions for agents. Defines when an agent may stage, commit, and push, and the commit message format. Applies whenever an agent is about to stage, commit, or push, or is writing or proposing a commit message, regardless of repo or language.
---

# Git Development

Each item below is a brief summary. Full rationale, decision text, and checklists are in the linked records under `references/`; read the full record before applying it. The `GIT-NNNN` numbering is specific to this skill.

## Behavior

- **GIT-0001** (`references/GIT-0001-COMMIT-AND-PUSH.md`) — Never commit or push unasked. When the user asks to commit without narrowing the scope, stage the files you modified, commit, and push; a narrower request limits that.
- **GIT-0002** (`references/GIT-0002-COMMIT-MESSAGE-FORMAT.md`) — A short subject line ending with a period, one blank line, then a detailed body explaining what changed and why.

## When applying a decision

1. Before any `git add`, `git rm`, `git mv`, `git commit`, or `git push`, read GIT-0001.
2. Before writing or proposing a commit message, read GIT-0002.
3. Both apply together when asked to commit: write a correctly formatted message, then stage, commit, and push as the request allows.
