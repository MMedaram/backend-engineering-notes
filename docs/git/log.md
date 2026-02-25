---
title: git log
parent: Git
nav_order: 2
---

# git log

---

## Purpose

`git log` is used to view the commit history of a repository.

It helps you:
- Identify commit hashes
- Understand change history
- Investigate bugs
- Track feature development
- Prepare for cherry-pick or revert operations

---

## Syntax

> git log

This shows:

- Full commit hash
- Author
- Date
- Commit message

**Example:**    

```text
commit 815991d41b0aad4754ce6bc0f85cfe7719030eea
Author: Mohan Medarametla <m**mohan@gmail.com>
Date:   Tue Feb 24 22:44:03 2026 +0530

    added java-8 index and optional

```

> By default, `git log` lists all commits reachable from your current branch in reverse chronological order.

To see all branches commits:

> git log --all

---

## Most Useful Variations

### 1. Short One-Line Format (Recommended)

> git log --oneline

**Example output:**

```
b44a233 Fix baseurl configuration
f1d7e21 trigger rebuild
591ba07 re-structured java-programs into java
815991d added java-8 index and optional
e2279e2 updated exception-handling-index.md
19435be added java 17 notes
```
This is the most commonly used format in real projects.

---

### 2. Show Log of a Specific Branch

> git log main --oneline

Useful when you are on another branch but want to inspect `main`.

---

### 3. Graph View (Visual Branch Structure)

> git log --graph --oneline --all

Shows branch merges visually.

Very useful for understanding:
- Merge commits
- Branch divergence
- Rebase results

---

### 4. Log for a Specific File

> git log --oneline path/to/File.java

Used when:
- Investigating a bug
- Backporting a fix
- Finding when a file was changed

---

### 5. Show Detailed Changes in Each Commit

> git log -p

Displays patch (actual code changes).

Useful for:
- Code review
- Understanding what changed exactly

---

### 6. Limit Number of Commits

> git log -n 5

or

> git log --oneline -5

Shows only last 5 commits.

---

### 7. Filter by Author

> git log --author="Mohan"

Useful in large team repositories.

---

### 8. Filter by Date

> git log --since="2 weeks ago"

> git log --until="2025-01-01"

---

## Real-World Backend Use Cases

1. Finding commit hash for cherry-pick
2. Identifying when a bug was introduced
3. Checking what went into a release
4. Reviewing changes before merging
5. Tracking work done in a sprint

---

## Pro-Level Combination

git log --graph --oneline --decorate --all

- --graph     → Visual branch structure
- --oneline   → Compact view
- --decorate  → Shows branch and tag names
- --all       → Includes all branches

This is extremely useful in real enterprise projects.


> `git log` is the primary command for exploring Git history.

