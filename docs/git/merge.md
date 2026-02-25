---
title: git merge
parent: Git
nav_order: 5
---

# git merge

---

## Purpose

`git merge` is used to combine changes from one branch into another branch.

It integrates the history of two branches.

Unlike rebase, merge does NOT rewrite history.

---

## Basic Syntax

> git merge <branch-name>

Example:

> git switch main

> git merge feature-branch

This merges feature-branch into main.

---

## What Happens Internally

Git finds the common ancestor between branches
and combines changes.

There are two main types of merge:

1. Fast-forward merge

2. Three-way merge (creates merge commit)

---

# 1. Fast-Forward Merge

Before:

main: A --- B

feature: A --- B --- C

If you merge feature into main:

> git switch main

> git merge feature

After:

main: A --- B --- C

- No merge commit created.    
- History remains linear.

---

# 2. Three-Way Merge (Non Fast-Forward)

Before:

main: A --- B --- C

feature: A --- B --- D --- E

After:

> git switch main

> git merge feature

Result:

A --- B --- C -------- M
\         /
D --- E

Git creates a merge commit (M).

This preserves branch history.

---

## Force Merge Commit (Even If Fast-Forward)

> git merge --no-ff feature-branch

Creates a merge commit even when fast-forward is possible.

Used when:
- You want clear branch history
- You want feature tracking

---

## Handling Merge Conflicts

If conflict occurs:

1. Git marks conflict in files
2. Manually resolve conflicts
3. Stage resolved files:

   git add .

4. Complete merge:

   git commit

To abort merge:

git merge --abort

---

## Common Real-World Backend Use Cases

1. Merging feature branch into main
2. Integrating release branch
3. Combining hotfix branch into production
4. Team collaboration workflow

---

## Merge vs Rebase

Merge:
- Preserves complete history
- Safe for shared branches
- Creates merge commits
- No history rewriting

Rebase:
- Rewrites history
- Cleaner linear history
- Dangerous if used on public branches

Rule of thumb:

Use merge for shared branches.
Use rebase for local feature cleanup.

---

## Common Mistakes

1. Merging without pulling latest main
2. Resolving conflicts incorrectly
3. Merging large outdated branches

---

## Best Practice in Enterprise Projects

Before merging:

> git fetch

>git switch feature-branch

>git rebase origin/main

Then:

> git switch main

> git merge feature-branch

This reduces merge conflicts and keeps history clean.

---

## Summary

`git merge` combines branches.

Types:
- Fast-forward merge
- Three-way merge (merge commit)

Safe for team environments.     
Does not rewrite history.