# Commit And Push

Commit and push only when the user asks for it. An explicit request is the approval.

## Rule

- Never commit or push on your own initiative. Finishing a change, or the user approving a change, a design decision, or a plan ("go ahead", "yes, do it"), is not a request to commit or push.
- When the user asks to commit without narrowing the scope (for example, "commit"), do all of the following without asking again: stage the files you modified, commit them, and push the current branch to its upstream.
- Stage only the files you modified. Leave other changes in the working tree and anything the user already staged untouched. Never use `git add -A`, `git add .`, or `git commit -a`.
- Follow any narrower scope the user states and do not go beyond it. For example, "only commit", "don't push", "commit what I staged", or "only these files" each limit what you do.
- After committing and pushing, report the commit hash, the branch, and the files included.
- If the commit would include more than you modified, or the push would need a force push or a new upstream, ask first.
- If the user explicitly asks for review first (for example, "show me before you commit"), show the changed files, the diff (or a faithful summary of it for large diffs), and the proposed commit message, then wait for approval. Approval covers only what was shown; ask again if anything changes.
