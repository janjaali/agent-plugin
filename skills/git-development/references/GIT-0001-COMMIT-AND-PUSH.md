# GIT-0001: Commit And Push Only When Asked, Staging Only Modified Files

- Status: Approved
- Date: 2026-10-25

## Context

An agent that commits or pushes on its own initiative takes actions the user did not request and cannot easily undo once they are shared. An agent that must ask for approval at every step, on the other hand, adds friction to the common case where the user has already said "commit".

Staging has the same tension. An agent that stages everything (`git add -A`, `git commit -a`) can sweep unrelated changes into a commit, while one that never stages forces the user to do it by hand before every commit.

## Decision

The agent commits and pushes only when the user asks for it, and an explicit request is the approval. When the request does not narrow the scope, the agent stages the files it modified, commits them, and pushes the current branch to its upstream.

### Required design rules

1. Never commit or push on your own initiative. Finishing a change, or the user approving a change, a design decision, or a plan ("go ahead", "yes, do it"), is not a request to commit or push.
2. When the user asks to commit without narrowing the scope (for example, "commit"), stage the files you modified, commit them, and push the current branch to its upstream without asking again.
3. Stage only the files you modified. Leave other changes in the working tree and anything the user already staged untouched. Never use `git add -A`, `git add .`, or `git commit -a`.
4. Follow any narrower scope the user states and do not go beyond it. For example, "only commit", "don't push", "commit what I staged", or "only these files" each limit what you do.
5. After committing and pushing, report the commit hash, the branch, and the files included.
6. If the commit would include more than you modified, or the push would need a force push or a new upstream, ask first.
7. If the user explicitly asks for review first (for example, "show me before you commit"), show the changed files, the diff (or a faithful summary of it for large diffs), and the proposed commit message, then wait for approval. Approval covers only what was shown; ask again if anything changes.

## Consequences

Positive:

- A plain "commit" completes the whole workflow in one step.
- Unrelated changes the user has in the working tree or index are never swept into a commit.
- The user stays in control, because nothing is committed or pushed unasked.

Trade-offs:

- A push happens without a separate confirmation, so the user must say "don't push" or "show me first" when they want a narrower action.
- The agent must track which files it modified, since it cannot rely on `git add -A`.

## Implementation checklist

1. Do not run `git commit` or `git push` unless the user has asked for it in this conversation.
2. Read the request for any narrower scope before staging.
3. Stage the files you modified by explicit path, and check `git status` to confirm nothing else is staged for the commit.
4. Write the commit message following GIT-0002.
5. Push the current branch to its upstream, unless that needs a force push or a new upstream, in which case ask first.
6. Report the commit hash, branch, and files included.
