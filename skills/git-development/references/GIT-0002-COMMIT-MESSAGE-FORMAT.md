# GIT-0002: Commit Messages Have A Period-Terminated Subject And A Detailed Body

- Status: Approved
- Date: 2026-10-25

## Context

Commit messages written without a convention vary from one-word summaries ("Update files", "Fix issue") to unstructured paragraphs. Short, generic messages say nothing about what changed or why, which makes history hard to search and review, while messages without a clear subject line render badly in log views and tooling that show only the first line.

## Decision

Every commit message has a short subject line ending with a period, followed by one blank line and a detailed body explaining what changed and why.

```text
<short commit message ending with a dot>

<detailed commit message explaining the what and why>
```

### Required design rules

1. Keep the subject concise and action-oriented while accurately describing the overall change.
2. End the subject with a period.
3. Separate the subject and body with exactly one blank line.
4. In the body, explain both the substantive changes and the motivation or behavior they address.
5. Prefer specific wording over generic messages such as "Update files" or "Fix issue".
6. Do not add unrelated implementation details or claims that are not supported by the change.

### Example

```text
Validate empty specification sections.

Reject specifications that omit required user stories or acceptance criteria so
invalid documents fail during validation instead of reaching code generation.
```

## Consequences

Positive:

- Log views show a meaningful one-line summary, and the body preserves the reasoning for later readers.
- Reviewers can understand the intent of a change without reconstructing it from the diff.

Trade-offs:

- Writing a real body takes more effort than a one-line message, even for small changes.
- The trailing period on the subject is a convention some tools and teams do not use.

## Implementation checklist

1. Write a concise subject that describes the overall change and ends with a period.
2. Leave exactly one blank line after the subject.
3. Write a body that explains what changed and why.
4. Check that the message contains no generic wording and no claims the change does not support.
