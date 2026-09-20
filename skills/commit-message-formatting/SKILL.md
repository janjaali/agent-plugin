---
name: commit-message-formatting
description: Formats commit messages with a concise subject line followed by a detailed explanation of what changed and why.
---

# Commit Message Formatting

Use this skill whenever creating or proposing a Git commit message.

## Required format

Every commit message must contain a short subject line ending with a period, followed by a blank line and a detailed body explaining what changed and why.

```text
<short commit message ending with a dot>

<detailed commit message explaining the what and why>
```

## Rules

- Keep the subject concise and action-oriented while accurately describing the overall change.
- End the subject with a period.
- Separate the subject and body with exactly one blank line.
- In the body, explain both the substantive changes and the motivation or behavior they address.
- Prefer specific wording over generic messages such as "Update files" or "Fix issue".
- Do not add unrelated implementation details or claims that are not supported by the change.

## Example

```text
Validate empty specification sections.

Reject specifications that omit required user stories or acceptance criteria so
invalid documents fail during validation instead of reaching code generation.
```
