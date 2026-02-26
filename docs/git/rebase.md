---
title: git rebase
parent: Git
nav_order: 3
---

# git rebase

---

## Purpose

`git rebase` is used to move or reapply commits from one branch onto another base branch.

It is mainly used to:
- Keep a clean, linear commit history
- Update a feature branch with latest changes from main
- Clean up commits before merging

---

## Basic Concept

Rebase takes your branch commits and replays them on top of another branch.

Instead of creating a merge commit (like `git merge`), rebase rewrites history to make it look linear.

---

## Syntax

> git rebase base-branch

Example:

> git switch feature-branch  

> git rebase main

This means: Take my feature-branch commits and replay them on top of main.

---

## What Happens Internally

Before rebase:

main:  A --- B --- C

feature: A --- B --- D --- E

After:

> git rebase main

Result:

main:
A --- B --- C

feature:
A --- B --- C --- D' --- E'

D and E are recreated as new commits (new hashes).

Important:     
Rebase creates new commit hashes because history is rewritten.

---

## Most Common Real-World Use Case

### Updating Feature Branch Before Merge with `main`

Workflow:

> git switch feature-branch  

> git fetch  
 
> git rebase origin/main

This ensures:
- Your branch is based on the latest main
- No unnecessary merge commits
- Cleaner pull request history

---

## Handling Conflicts During Rebase

If conflict occurs:

1. Fix conflict manually in files
2. Stage changes:
   > git add .

3. Continue rebase:
   > git rebase --continue

If you want to cancel:
   > git rebase --abort

If you want to skip a problematic commit:
   > git rebase --skip

---

## Important Rule

Never rebase a public/shared branch.

Safe:
- Rebasing your local feature branch

**Dangerous:**
- Rebasing main
- Rebasing a branch already pushed and used by others

---

## Interactive Rebase (Advanced)

> git rebase -i base-branch

Used to:
- Squash commits
- Reorder commits
- Edit commit messages
- Remove commits

Example:

> git rebase -i HEAD~3

This allows editing last 3 commits.

---

## When to Use Rebase

- Before opening a pull request
- To clean up messy commit history
- To sync feature branch with main
- To squash multiple small commits

---

## Recovery Tip

If something goes wrong:

> git reflog

You can restore previous state:

> git reset --hard old-commit

Reflog is your safety net after rebase mistakes.

---

## Summary

`git rebase` replays your commits on top of another branch.

Benefits:
- Cleaner history
- Better pull requests
- Linear commit structure

Risk:
- History rewriting
- Should not be used carelessly in team environments

