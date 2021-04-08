# Code Review

The JORE4 development team has defined code review as a mandatory requirement for the Definition of Done (DoD). We have agreed on the following practices for code reviewing.

## Reviewable.io

As of now, we are using Reviewable.io to expand our options for code reviews. It's not a bulletproof system, but it allows us to be able to somewhat keep track of review comments even after some change has been applied to the pull request (PR). However the development team is still missing many features that Gerrit would allow (reviewing commits separately, testing commits separately, keeping track of changes for each commit, etc.), so the search is not over for a better code review setup.

### Revisions

Reviewable.io creates _snapshotted revisions_ of PRs (_r1_, _r2_..), which allows reviewers to see how the PR has evolved during the review process. Unlike Gerrit, which only looks at the commits, Reviewable.io also considers reviewers and their actions when deciding when to create a new snapshot: multiple iterations of the same PR may be merged into a single revision, if e.g. no reviewer has looked at the PR or made any comments. This makes life easier for the reviewers as they don't need to look at intermediate changes which have already been overwritten. However, this also means that only certain versions of the PR are persisted instead of storing each and every version of each commit in the PR.

Reviewable.io offers to modes for reviews: _combine commits for review_ and _review each commit separately_, which affects slightly how revisions are created when the PR contains multiple commits. The former tries to merge several commits to the same revision while the latter is more eager in creating new revisions if new commits are added to the PR. It is yet unclear which one is the better choice.

### Reviewable.io Settings

1. To see a pull request in Rewiewable.io, click the purple button in the pull request description.
1. To see the JORE4 team's all pull requests, remember to authorize it on https://reviewable.io/reviews by pressing the "See Team's Pull Requests" green button.

## Git Repository Settings

For all JORE4 git repositories, the following Settings should be set up:

_Git Repository / Settings / Options / Merge Button_

1. Should disable merge commits as we wish to keep the main branch linear.

_Git Repository / Settings / Branches_

1. Should set the `main` branch as protected
1. Should require at least 1 reviewer to accept the changes for the pull request
1. Should require status checks to pass before merging
1. Should require linear history
1. Should require to dismiss stale pull request approvals when new commits are pushed
1. Should require to enforce all configured restrictions above for administrators

## Code Review Process

1. We aim to keep the code review conversations in English
1. The pull request creator manually assigns reviewer(s) to the pull request who fit the subject the best
1. In case it's not clear who is most knowledgable in the given subject, we ask on Slack or assign the whole jore4 team as a reviewer
1. If the pull request is not complete, it is marked as a draft
1. We aim to keep conversations about code reviews in Reviewable.io instead of Slack. (If you are in a hurry, you could send a Slack DM to the reviewer to notify about a new pull request or ask about some details. Otherwise refrain from spamming the Slack channel)
1. When a pull request is approved, it can be rebased to the main branch. However even if some trivial changes are asked to be done by the reviewer, aim to get another approval before rebasing the PR to the main branch
