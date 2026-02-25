---
title: git cherry-pick
parent: Git
nav_order: 1
---

# git cherry-pick

---

## Purpose

Apply a specific commit from one branch into another branch.

It is used when you do NOT want to merge the full branch, but only need selected changes.

---

## Syntax

> git cherry-pick <commit-hash>

Example:

> git cherry-pick a1b2c3d

The common ways to get <commit-hash>.

> git log main --oneline

---

## What Actually Happens Internally

- Git takes the changes introduced by that commit
- Re-applies those changes on your current branch
- Creates a NEW commit with a new commit hash

Important:
Cherry-pick does NOT copy the original commit object.
It creates a new commit with the same changes.

---

## Real-World Use Cases

### 1. Backporting a Bug Fix

Branch structure:

main:
A --- B --- C (bug fix)

release-1.0:
A --- B

You need the bug fix from main but do not want other changes.

Solution:

> git switch release-1.0

> git cherry-pick <hash-of-C>

---

### 2. Moving a Small Fix Between Feature Branches

You accidentally committed a fix in feature-A,
but it actually belongs to feature-B.

Solution:

> git switch feature-B

> git cherry-pick <commit-hash>

---

## Cherry-Picking Multiple Commits

Cherry-pick multiple specific commits:

> git cherry-pick <commit-hash1> <commit-hash2> <commit-hash3>

Cherry-pick a range:

> git cherry-pick <start-commit>^..<end-commit>

Example:

> git cherry-pick a1b2c3d^..d4e5f6g

---

## Handling Conflicts

If conflict happens:

1. Fix conflicts manually in files
2. Stage resolved files:

> git add .

3. Continue:

> git cherry-pick --continue

If you want to cancel:

> git cherry-pick --abort

---

## How to Detect Duplicate Cherry-Pick

Git may say:

"the previous cherry-pick is now empty"

This means:
The changes already exist in this branch.

You can skip it:

> git cherry-pick --skip

---

## When NOT to Use Cherry-Pick

Do NOT use cherry-pick when:

- You need full branch changes
- You want to preserve branch history relationships
- You are merging long-running feature branches

Instead use:
- git merge
- git rebase

---

## Best Practices (Enterprise Projects)

- Use cherry-pick mainly for:
    - Hotfixes
    - Backports
    - Small independent commits

- Avoid cherry-picking commits with:
    - Large refactors
    - Database migrations tied to multiple commits
    - Massive file renames

- Always check commit dependencies before cherry-picking.

---

## Advanced Options

Cherry-pick without auto commit:

> git cherry-pick -n <commit-hash>

- This applies changes but does NOT create a commit.
- Useful when combining multiple commits manually.

---

> Cherry-pick = apply specific commit changes onto your current branch.
