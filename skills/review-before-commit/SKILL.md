---
name: review-before-commit
description: Requires the user to review changes before anything is committed or pushed. Applies whenever an agent is about to run git commit or git push, regardless of repo or language.
---

# Review Before Commit

Never commit or push without the user's explicit approval for that specific commit or push.

## Rule

- Before committing, show the user what will be committed: the list of changed files, the diff (or a faithful summary of it for large diffs), and the proposed commit message. Then stop and wait for approval.
- Before pushing, tell the user which branch and remote will be pushed to and which commits are included, then wait for approval.
- Treat approval as covering only what was shown. If the changes or message change afterwards, ask again.
- Approval of a change, a design decision, or a plan ("go ahead", "yes, do it") is not approval to commit or push it. Only an explicit request to commit or push counts.
- If the user explicitly says to skip review (for example, "commit and push without asking from now on"), follow that instruction for the scope they stated and revert to this rule afterwards.
- Never stage files (no `git add`, `git rm`, `git mv`, `git commit -a`) unless the user explicitly asks. The user stages while reviewing; leave changes in the working tree and let them stage what belongs in the commit.
- When asked to commit, commit only what the user has already staged. If nothing is staged, say so and ask instead of staging anything yourself.
