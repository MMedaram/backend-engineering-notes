---
title: Git
parent: Backend Engineering Notes
nav_order: 8
---


# Git Commands

---

### git init

Initializes a new Git repository in the current folder.

### git clone `repo-url`
Clones a remote Git repository into your local machine.

### git status
Shows the state of the working directory and staging area.

### git add `file`
Stages a file for commit.

### git add .
Stages all changes (new, modified, deleted files).


### git commit -m "message"
Commits the staged changes with a message.

### git commit -am "message"
Adds and commits tracked files in one command.

### git log
Shows the commit history.

### git diff
Shows changes between the working directory and the last commit.


### git diff --staged
Shows changes between staged files and last commit.

---

## BRANCHING & MERGING

### git branch
Lists all branches.

### git branch `branch-name`
Creates a new branch.

### git checkout `branch-name`
Switches to a specific branch.

### git switch `branch-name`
Newer way to switch branches.

### git switch -c `new-branch`
Creates and switches to a new branch.

### git merge `branch`
Merges another branch into the current branch.

### git branch -d `branch`
Deletes a local branch.

### git branch -D `branch`
Force deletes a local branch.

### git merge --abort
Cancels a merge if there are conflicts.

### git stash
Temporarily stores changes in working directory.

---

## REMOTE REPOSITORY MANAGEMENT

### git remote -v
Shows URLs of remotes (like origin).

### git remote add origin `url`
Links local repo to a remote.

### git push -u origin main
Pushes changes and sets the upstream.

### git push
Pushes commits to the remote branch.

### git pull
Fetches and merges remote changes.

### git fetch
Downloads updates from remote but doesn't merge.

### git push origin `branch-name`
Pushes a specific branch to remote.

### git push --force
Forces a push (used carefully!).

### git push origin --delete `branch`
Deletes a remote branch.

### git pull origin `branch`
Pulls changes from specific remote branch.


---

## RESET, REVERT, CLEANUP

### git reset `file`
Unstages a file.

### git reset --hard
Resets working directory and index to last commit.

### git reset --soft HEAD~1
Moves HEAD back but keeps changes staged.

### git revert `commit`
Creates a new commit that undoes a previous one.

### git clean -f
Deletes untracked files.

### git clean -fd
Deletes untracked files and directories.

---

## TAGS, LOGS, HISTORY

### git tag
Lists all tags.

### git tag `tag-name`
Creates a tag.

### git tag -a v1.0 -m "Version 1"
Creates an annotated tag.

### git show `tag`
Shows tag details and associated commit.

### git log --oneline
Shows simplified commit history.

### git log --graph
Displays commits as a graph.

---

## TOOLS & CONFIGURATION

### git config --global user.name "Your Name"
Sets global username.

### git config --global user.email "you@example.com"
Sets global email.

### git config --list
Lists current configurations.

### git blame `file`
Shows who changed which line of a file.

### git cherry-pick `commit`
Applies a commit from another branch.

### git rebase `branch`
Reapplies commits on top of another base tip.

### git reflog
Shows the history of branch movements.

### git archive
Creates a zip/tar archive of the repository content.
