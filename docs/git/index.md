---
title: Git
parent: Backend Engineering Notes
nav_order: 8
---


# Git Commands

---

#  Daily Developer Commands (Must Master)

These commands are used almost every day in real-world projects.

## 1. Repository Setup
- git init
- git clone
- git config
- .gitignore basics

## 2. Checking & Inspecting Changes
- git status
- git log
- git diff
- git show

## 3. Staging & Committing
- git add
- git add -p (partial staging)
- git commit
- git commit --amend

## 4. Branching Basics
- git branch
- git switch (or checkout)
- git merge
- git log --graph --oneline --all

## 5. Remote Basics
- git remote
- git fetch
- git pull
- git push

## 6. Temporary Work
- git stash
- git stash pop
- git stash apply

---

# Professional Team Usage

Important for real enterprise environments and clean collaboration.

## 1. History Management
- git rebase
- git rebase -i (interactive)
- git squash (via rebase)
- fixup commits

## 2. Selective Commit Operations
- git cherry-pick
- cherry-pick ranges

## 3. Undo & Recovery
- git reset (soft, mixed, hard)
- git revert
- git restore
- git clean
- git reflog

## 4. Remote Collaboration Advanced
- upstream tracking branches
- git push --force-with-lease
- resolving diverged branches
- handling rejected pushes

## 5. Release Handling
- git tag
- annotated vs lightweight tags
- pushing tags

---

# Advanced & Debugging Mastery

Power-user and troubleshooting tools.

## 1. Investigation & Debugging
- git blame
- git bisect
- advanced log filtering
- searching commit history

## 2. Patch & Backport Workflow
- git format-patch
- git apply

## 3. Large Repository Handling
- shallow clone
- sparse checkout
- Git LFS (concept)

## 4. Internals Understanding
- Working tree
- Staging area (Index)
- HEAD
- Commit objects (blob, tree)
- Detached HEAD state

## 5. Automation
- Git hooks
- pre-commit hooks
- commit message validation

