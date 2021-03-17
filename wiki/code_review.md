# Code Review

The JORE4 development team has defined code review as a mandatory requirement for the Definition of Done (DoD). We have agreed on the following practices for code reviewing.

## Reviewable.io

As of now, we are using Reviewable.io to expand our options for code reviews. It's not a bulletproof system, but it allows us to be able to somewhat keep track of review comments even after some change has been applied to the pull request. However the development team is still missing many features that Gerrit would allow (reviewing commits separately, testing commits separately, keeping track of changes for each commit, etc.), so the search is not over for a better code review setup.

## Reviewable.io Settings

1. To see a pull request in Rewiewable.io, click the purple button in the pull request description.
2. To see the JORE4 team's all pull requests, remember to authorize it on https://reviewable.io/reviews by pressing the "See Team's Pull Requests" green button.
3. When a pull request is opened for a repository, should choose "Review each commit separately" from the rightmost dropdown list on the top on the page

## Git Repository Settings

For all JORE4 git repositories, the following Settings should be set up:

_Git Repository / Settings / Merge Button_
1. Should disable merge commits as we wish to keep the main branch linear.

_Git Repository / Settings / Branches_
1. Should set the `main` branch as protected
2. Should require at least 1 reviewer to accept the changes for the pull request
3. Should require status checks to pass before merging
4. Should require linear history
5. Should require to dismiss stale pull request approvals when new commits are pushed

## Code Review Process

1. We aim to keep the code review conversations in English
2. The pull request creator manually assigns reviewer(s) to the pull request who fit the subject the best
3. In case it's not clear who is most knowledgable in the given subject, we ask on Slack or assign the whole jore4 team as a reviewer
4. If the pull request is not complete, it is marked as a draft
5. We aim to keep conversations about code reviews in Reviewable.io instead of Slack. (If you are in a hurry, you could send a Slack DM to the reviewer to notify about a new pull request or ask about some details. Otherwise refrain from spamming the Slack channel)
6. When a pull request is approved, it can be rebased to the main branch. However even if some trivial changes are asked to be done by the reviewer, aim to get another approval before rebasing the PR to the main branch
