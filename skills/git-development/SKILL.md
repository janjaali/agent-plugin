---
name: git-development
description: Git conventions for agents. Defines when an agent may stage, commit, and push, and the commit message format. Applies whenever an agent is about to stage, commit, or push, or is writing or proposing a commit message, regardless of repo or language.
---

# Git Development

Each item below is a brief summary. The full rules are in the linked files under `references/`; read the full file before acting.

## Behavior

- **Commit and push** (`references/COMMIT-AND-PUSH.md`) — Never commit or push unasked. When the user asks to commit without narrowing the scope, stage the files you modified, commit, and push; a narrower request limits that.
- **Commit message format** (`references/COMMIT-MESSAGE-FORMAT.md`) — A short subject line ending with a period, one blank line, then a detailed body explaining what changed and why.

## When applying

1. Before any `git add`, `git rm`, `git mv`, `git commit`, or `git push`, read `references/COMMIT-AND-PUSH.md`.
2. Before writing or proposing a commit message, read `references/COMMIT-MESSAGE-FORMAT.md`.
3. Both apply together when asked to commit: write a correctly formatted message, then stage, commit, and push as the request allows.
