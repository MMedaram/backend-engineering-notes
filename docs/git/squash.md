---
title: git squash
parent: Git
nav_order: 4
---

# Git Squash

---

**Important clarification:**    
There is no direct command called  `git squash`

---

## Purpose

Squashing means combining multiple commits into a single commit.

It is used to:
- Clean messy commit history
- Combine small incremental commits
- Prepare a clean pull request
- Maintain professional repository history

---

## Method 1: Squash Using Interactive Rebase (Most Common)

### Basic Syntax

> git rebase -i HEAD~<number_of_commits>

Example:

> git rebase -i HEAD~3

This opens an editor showing:

pick a1b2c3d Add validation    
pick e4f5g6h Fix typo  
pick x7y8z9k Improve logging

To squash commits:

Change this:

pick e4f5g6h Fix typo  
pick x7y8z9k Improve logging

To this:

squash x7y8z9k Improve logging

Save and exit.

Result:
All 3 commits become a single commit.

---

### What Happens Internally

- Git combines changes of selected commits
- Creates one new commit
- Old commits are removed from history
- Commit hash changes

History is rewritten.

---

### Real-World Backend Use Case

Feature branch commits:

Add API  
Fix null pointer  
Fix logging  
Fix typo  
Update comments

Before raising PR, squash them into:

> Add Payment API with validation

This keeps history clean and professional.

---

### Conflict Handling During Squash

If conflicts occur:

1. Fix conflicts manually
2. git add .
3. git rebase --continue

To cancel:

git rebase --abort

---

## Method 2: Squash Using Merge

### Syntax

> git merge --squash feature-branch

This:
- Applies all changes from feature branch
- Does NOT create merge commit
- Creates a single commit manually

After squash merge:

> git commit -m "Add complete feature"

---

## Difference Between Rebase Squash and Merge Squash

Rebase squash:
- Rewrites branch history
- Used before pushing or PR

Merge --squash:
- Used when merging feature into main
- Keeps main branch clean
- Does not preserve individual commits

---

## When to Use Squash

- Before creating pull request
- Cleaning up small commits
- Combining fix commits into main feature commit

---

## When NOT to Use Squash

- If individual commits must remain separate
- If commits represent logically independent changes
- On shared branches already used by team

---

## Summary

Squashing = Combining multiple commits into one.

Two ways:
1. git rebase -i (most common)
2. git merge --squash

Use squash to maintain clean and professional Git history.