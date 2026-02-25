---
title: git tag
parent: Git
nav_order: 6
---

# git tag

## Purpose

`git tag` is used to mark a specific commit as important.

Most commonly used to mark:
- Production releases
- Stable builds
- Version milestones (e.g., v14.8.0, v15.0.0)

Tags are permanent references to specific commits.

---

## Why Tags Are Important

Branches move forward with new commits.
Tags do NOT move.

A tag always points to the exact commit where it was created.

This makes it perfect for versioning and releases.

---

# Types of Tags

There are two types:

1. Lightweight tag
2. Annotated tag (recommended for releases)

---

## 1. Lightweight Tag

Basic syntax:

> git tag v14.8.0

This simply creates a pointer to the current commit.

No metadata stored.

Used for:
- Temporary marking
- Local tagging

---

## 2. Annotated Tag (Recommended for Production)

Basic syntax:

> git tag -a v14.8.0 -m "Release version 14.8.0"

This stores:
- Tag name
- Tag message
- Tagger name
- Date

Annotated tags are best practice for release management.

---

# Tag a Specific Commit

If you want to tag an older commit:

> git tag -a v14.8.0 <commit-hash> -m "Release version 14.8.0"

Example:

> git tag -a v14.8.0 a1b2c3d -m "Release 14.8.0"

---

# View Tags

List all tags:

> git tag

Show tag details:

> git show v14.8.0

---

# Push Tags to Remote

Tags are NOT pushed automatically with git push.

Push specific tag:

> git push origin v14.8.0

Push all tags:

> git push origin --tags

Important:
If you forget to push the tag, CI/CD will not detect the release.

---

# Delete a Tag

Delete locally:

> git tag -d v14.8.0

Delete from remote:

> git push origin --delete v14.8.0

---

# Checkout a Tag

git checkout v14.8.0

This puts you in "detached HEAD" state.

You are not on a branch.
You are viewing that exact historical commit.

To create a branch from tag:

> git checkout -b hotfix-14.8.1 v14.8.0

---

# Real-World Backend Use Case

Typical release workflow:

1. Update version in pom.xml to 14.8.0
2. Commit changes
3. Create annotated tag:

> git tag -a v14.8.0 -m "Release 14.8.0"

4. Push code and tag:

>  git push origin main
  
> git push origin v14.8.0

5. CI/CD builds artifact:
   service-14.8.0.jar

Tag = exact production code reference.

---

# Best Practices

- Always use annotated tags for releases
- Follow consistent naming convention (vMajor.Minor.Patch)
- Never move or modify production tags
- Treat release tags as immutable

---

# Common Mistakes

1. Forgetting to push tags
2. Using lightweight tags for production
3. Re-tagging the same version improperly
4. Deleting tags without coordination

---

### Q: Do tags change when new commits are added?

No. Tags always point to the same commit.

### Q: What is the difference between branch and tag?

Branch:
- Moves forward as commits are added.

Tag:
- Fixed reference to a specific commit.

---

# Scenario

- very old branch: release/14.6 ❌ deleted 
- But tag exists: v14.6.0 ✅
- Now you need to apply a fix to that old version.

### Step 1 – Create a New Branch from the Tag
 
> git checkout -b hotfix-14.6.1 v14.6.0

Now:

- You are working from the exact code of version 14.6.0
- Even though the original branch was deleted
- You recreated a working line from that historical snapshot

### Step 2 – Apply Your Fix

> git add .

> git commit -m "Fix critical issue in 14.6"

### Step 3 – Create New Patch Tag

> git tag -a v14.6.1 -m "Patch release 14.6.1"

> git push origin hotfix-14.6.1

> git push origin v14.6.1

---
# Summary

`git tag` is used to mark release versions in Git.

Types:
- Lightweight (simple pointer)
- Annotated (recommended for releases)

Tags connect:
Git commit history
with
Application versioning and production releases.