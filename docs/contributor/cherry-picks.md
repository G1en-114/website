---
title: How to cherry-pick PRs
sidebar_label: Cherry Picks
---

This document explains how cherry picks are managed on release branches in the `Project-HAMi/HAMi` repository. The typical use case is backporting a bug fix from master to an active release branch.

## Prerequisites

- A pull request already merged into master.
- The target release branch exists (for example, `release-v2.11`).
- Git and GitHub configured with your fork as `origin` and the upstream as `upstream`.

## What qualifies for a cherry pick

Release branches receive far fewer merges than master because the bar is higher. Cherry picks are reserved for critical fixes only:

- Data loss
- Memory corruption
- Panic, crash, or hang
- Security vulnerabilities

A bug that affects only an alpha feature does not qualify, even if it is a real bug.

If the fix is not clearly critical, support the case with:

- A GitHub issue describing the problem
- Scope of the change
- Risks of adding the change
- Risks of regression
- Tests added or updated
- Sign-off from key stakeholders or reviewers

Features that were not enabled on a specific vendor's platform belong in master for the next release. They will not be backported.

## Initiate a cherry pick

From your HAMi checkout, create a branch from the target release branch. This example uses `release-v2.11`. Replace `MERGED_COMMIT_SHA` with the commit that contains the fix on `master`. For a squash-merged PR, use the squash commit.

```bash
git fetch upstream
git switch -c backport-fix-v2.11 upstream/release-v2.11
git cherry-pick -x -s MERGED_COMMIT_SHA
git push -u origin backport-fix-v2.11
```

Open a PR from your fork's `backport-fix-v2.11` branch into `Project-HAMi/HAMi:release-v2.11` and link the original PR. Use a separate branch and PR for each target release branch.

## Cherry pick review

Cherry pick PRs follow the same review process as normal PRs. Code owners review (`/lgtm`) and approve (`/approve`) as they see fit.

Follow the normal release note requirements and refer to the original PR for context.

## Troubleshooting

**The cherry pick does not apply cleanly.** Resolve the conflicts, stage the resolved files with `git add`, and run `git cherry-pick --continue` before pushing the branch.

**CI fails on the cherry pick branch.** Fix the failure on your backport branch and push the updated branch to your fork.

## Unsupported releases

Fixes for end-of-life release branches are not accepted without prior discussion. Open an issue to start that conversation before submitting a cherry pick against an unsupported branch.
