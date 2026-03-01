---
name: ookami-git-protocol
description: >
  ookami team git and branch workflow conventions. Use this skill whenever
  creating or naming branches, writing commit messages, creating or merging
  pull requests, rebasing, squashing commits, or performing any git operations.
  Also use when the user asks about branch naming conventions, commit message
  format, PR workflow, merge strategy, or GitHub Flow practices.
---

# Git Protocol

Follow these conventions for all git operations.
Full reference: https://github.com/ookamiinc/guides/blob/master/protocol/git.md

We use [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html).

## Language

- Use American English in all commit messages, branch names, and PR descriptions.

## Repository Maintenance

- Do not include files specific to your development machine or process in source control.
- Delete local and remote feature branches after merging.
- Perform work in a feature branch.
- Rebase frequently to incorporate upstream changes.
- Use pull requests for code reviews.
- Keep commits and pull requests as small as possible.
- Do not make changes to multiple concerns in a single pull request.

## Branch Naming

- Use lowercase.
- Use dashes (not underscores) between words.
- Use a descriptive name for what you are going to do.

## Commit Messages

Write commit messages in this format:

```
Present-tense summary under 50 characters

* More information about commit (under 72 characters).
* More information about commit (under 72 characters).

http://project.management-system.com/ticket/123
```

- Use present tense for the summary line.
- Keep the summary under 50 characters.
- Keep bullet points under 72 characters.
- Include a link to the relevant ticket/story when applicable.

## Feature Workflow

1. Create a local feature branch based off master (or staging if it exists).
2. Rebase frequently to incorporate upstream changes.
3. When feature is complete and tests pass, stage and commit changes.
4. If multiple commits exist, use interactive rebase to squash them into cohesive commits with good messages.
5. Push the branch and submit a pull request.
6. Ask for a code review.

## Merging

1. Rebase interactively — squash commits like "Fix whitespace" into meaningful commits. Edit commit messages to reveal intent.
2. Run tests.
3. Force push your branch with `--force-with-lease`.
4. Merge via the "Merge pull request" button on GitHub or via hub commands.
5. Pull master locally to stay up to date.
6. Delete the remote and local feature branches.
