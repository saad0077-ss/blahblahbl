# Git Commands — Complete Reference Guide

A reference README covering essential Git commands organized by category, with explanations and examples.

---

## Table of Contents
1. [What is Git](#what-is-git)
2. [Setup & Configuration](#setup--configuration)
3. [Creating & Cloning Repositories](#creating--cloning-repositories)
4. [Basic Workflow (Add, Commit, Status)](#basic-workflow-add-commit-status)
5. [Viewing History & Logs](#viewing-history--logs)
6. [Branching](#branching)
7. [Merging](#merging)
8. [Rebasing](#rebasing)
9. [Remote Repositories](#remote-repositories)
10. [Pushing & Pulling](#pushing--pulling)
11. [Undoing Changes](#undoing-changes)
12. [Stashing](#stashing)
13. [Tags](#tags)
14. [Diffing & Comparing](#diffing--comparing)
15. [Cherry-pick](#cherry-pick)
16. [Reflog](#reflog)
17. [Submodules](#submodules)
18. [Tracking & Ignoring Files](#tracking--ignoring-files)
19. [Aliases](#aliases)
20. [Common Workflows](#common-workflows)
21. [Common Git Errors & Fixes](#common-git-errors--fixes)

---

## What is Git

Git is a **distributed version control system** used to track changes in source code during software development. It allows multiple developers to work on a project simultaneously, maintains a full history of changes, and supports branching/merging for parallel development.

- **Distributed**: every developer has a full copy of the repository (including history), not just the latest snapshot.
- **Local repository**: `.git` folder created on `git init`.
- **Remote repository**: hosted version on GitHub/GitLab/Bitbucket etc.

---

## Setup & Configuration

```bash
git --version                          # check installed version

git config --global user.name "Tom"               # set global username
git config --global user.email "tom@email.com"    # set global email

git config --list                      # view all config settings
git config --global core.editor "code --wait"      # set default editor (VS Code)

git config --global alias.co checkout  # create a shortcut alias
```

---

## Creating & Cloning Repositories

```bash
git init                               # initialize a new local repo
git init my-project                    # initialize in a new folder

git clone <repo-url>                   # clone a remote repo
git clone <repo-url> my-folder         # clone into a specific folder name
git clone --depth 1 <repo-url>         # shallow clone (latest commit only)
git clone -b <branch> <repo-url>       # clone a specific branch
```

---

## Basic Workflow (Add, Commit, Status)

```bash
git status                             # show changed/staged/untracked files

git add file.txt                       # stage a specific file
git add .                              # stage all changes in current directory
git add -A                             # stage all changes (including deletions)
git add -p                             # interactively stage chunks of changes

git commit -m "Commit message"         # commit staged changes with a message
git commit -am "msg"                   # stage tracked files + commit in one step
git commit --amend -m "New message"    # edit the last commit's message
git commit --amend --no-edit           # add staged changes to last commit, same message
```

---

## Viewing History & Logs

```bash
git log                                # full commit history
git log --oneline                      # condensed, one line per commit
git log --oneline --graph --all        # visual branch graph
git log -p                             # show diffs with each commit
git log -n 5                           # show last 5 commits
git log --author="Tom"                 # filter by author
git log --since="2024-01-01" --until="2024-06-01"  # filter by date range
git log -- file.txt                    # history of a specific file

git show <commit-hash>                 # show details of a specific commit
git blame file.txt                     # show who changed each line & when
```

---

## Branching

```bash
git branch                             # list local branches
git branch -a                          # list all branches (local + remote)
git branch <branch-name>               # create a new branch
git branch -d <branch-name>            # delete a branch (safe — must be merged)
git branch -D <branch-name>            # force-delete a branch
git branch -m old-name new-name        # rename current branch

git checkout <branch-name>             # switch to a branch
git checkout -b <branch-name>          # create + switch to new branch

git switch <branch-name>               # modern alternative to checkout (switch only)
git switch -c <branch-name>            # create + switch (modern alternative)
```

---

## Merging

```bash
git merge <branch-name>                # merge a branch into the current branch
git merge --no-ff <branch-name>        # force a merge commit (no fast-forward)
git merge --abort                      # cancel a merge with conflicts
git merge --squash <branch-name>       # combine all commits from branch into one
```

**Conflict resolution:**
```bash
# After a conflict appears:
# 1. Open the conflicted file(s), resolve <<<< ==== >>>> markers
git add <resolved-file>
git commit -m "Resolve merge conflict"
```

---

## Rebasing

`rebase` re-applies commits from one branch on top of another, creating a **linear history** (vs. merge, which preserves branching history).

```bash
git rebase <branch-name>               # rebase current branch onto another
git rebase -i HEAD~3                   # interactive rebase — last 3 commits (squash/edit/reorder)
git rebase --continue                  # continue after resolving a conflict
git rebase --abort                     # cancel the rebase, return to original state
git rebase --skip                      # skip the current commit during rebase

git pull --rebase                      # pull + rebase instead of merge
```

⚠️ Never rebase commits that have already been pushed and shared with others — it rewrites history.

---

## Remote Repositories

```bash
git remote -v                          # list remotes with URLs
git remote add origin <repo-url>       # add a new remote named "origin"
git remote remove origin               # remove a remote
git remote rename origin upstream      # rename a remote
git remote show origin                 # show detailed info about a remote
```

---

## Pushing & Pulling

```bash
git push origin <branch-name>          # push branch to remote
git push -u origin <branch-name>       # push + set upstream tracking
git push --force                       # force push (overwrites remote — use with caution)
git push --tags                        # push all tags
git push origin --delete <branch-name> # delete a remote branch

git pull                               # fetch + merge from tracked remote branch
git pull origin <branch-name>          # pull a specific branch
git pull --rebase                      # pull and rebase instead of merge

git fetch                              # download remote changes without merging
git fetch --all                        # fetch from all remotes
git fetch --prune                      # remove remote-tracking refs that no longer exist
```

---

## Undoing Changes

```bash
git restore file.txt                   # discard unstaged changes in a file (modern)
git checkout -- file.txt               # discard unstaged changes (legacy)

git restore --staged file.txt          # unstage a file (keep changes)
git reset file.txt                     # unstage a file (legacy)

git reset --soft HEAD~1                # undo last commit, keep changes staged
git reset --mixed HEAD~1               # undo last commit, keep changes unstaged (default)
git reset --hard HEAD~1                # undo last commit, DISCARD all changes

git revert <commit-hash>               # create a new commit that undoes a previous commit (safe for shared history)
git revert -n <commit-hash>            # revert without auto-committing
```

---

## Stashing

Temporarily saves uncommitted changes so you can switch context, without committing incomplete work.

```bash
git stash                              # stash current changes
git stash save "message"               # stash with a description
git stash -u                           # include untracked files
git stash list                         # list all stashes
git stash show                         # show changes in the latest stash
git stash apply                        # apply latest stash, keep it in the list
git stash apply stash@{2}              # apply a specific stash
git stash pop                          # apply latest stash AND remove it from the list
git stash drop                         # delete the latest stash
git stash drop stash@{1}               # delete a specific stash
git stash clear                        # delete all stashes
```

---

## Tags

Used to mark specific commits as important — typically for **releases** (e.g., `v1.0.0`).

```bash
git tag                                # list all tags
git tag v1.0.0                         # create a lightweight tag
git tag -a v1.0.0 -m "Release version 1.0.0"  # create an annotated tag (with metadata)
git tag -d v1.0.0                      # delete a local tag

git push origin v1.0.0                 # push a single tag
git push origin --tags                 # push all tags
git push origin --delete v1.0.0        # delete a remote tag

git checkout v1.0.0                    # checkout code at a specific tag
```

---

## Diffing & Comparing

```bash
git diff                               # unstaged changes vs last commit
git diff --staged                      # staged changes vs last commit
git diff <branch1> <branch2>           # compare two branches
git diff <commit1> <commit2>           # compare two commits
git diff HEAD~1 HEAD                   # compare last commit with the one before
git diff -- file.txt                   # diff for a specific file
```

---

## Cherry-pick

Applies a **specific commit** from one branch onto another, without merging the whole branch.

```bash
git cherry-pick <commit-hash>          # apply a single commit to current branch
git cherry-pick <hash1> <hash2>        # apply multiple commits
git cherry-pick --continue             # continue after resolving conflicts
git cherry-pick --abort                # cancel the cherry-pick
```

---

## Reflog

Tracks **every movement of HEAD** (commits, resets, checkouts) — a safety net to recover "lost" commits even after a hard reset.

```bash
git reflog                             # show history of HEAD movements
git reset --hard HEAD@{2}              # recover to a previous HEAD state
```

---

## Submodules

Allows including another Git repository as a subdirectory inside your project (e.g., shared libraries).

```bash
git submodule add <repo-url> path/to/submodule  # add a submodule
git submodule init                     # initialize submodules after cloning
git submodule update                   # fetch submodule content
git clone --recurse-submodules <repo-url>  # clone repo + all submodules together
git submodule update --remote          # pull latest changes into submodule
```

---

## Tracking & Ignoring Files

```bash
# .gitignore file — list patterns of files/folders to ignore
# Example .gitignore content:
# build/
# *.log
# .env
# node_modules/

git rm --cached file.txt               # stop tracking a file (keep it locally)
git rm file.txt                        # remove a file from repo + filesystem
git rm -r folder/                      # remove a folder recursively

git check-ignore -v file.txt           # debug why a file is/isn't ignored
```

---

## Aliases

Custom shortcuts for frequently used commands.

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm "commit -m"
git config --global alias.lg "log --oneline --graph --all"

# Usage:
git st     # same as git status
git lg     # pretty graph log
```

---

## Common Workflows

### Starting a new feature
```bash
git checkout main
git pull origin main
git checkout -b feature/login-page
# ... make changes ...
git add .
git commit -m "Add login page UI"
git push -u origin feature/login-page
# open a Pull Request on GitHub/GitLab
```

### Updating your branch with latest main
```bash
git checkout feature/login-page
git fetch origin
git rebase origin/main      # or: git merge origin/main
```

### Squashing commits before a PR
```bash
git rebase -i HEAD~5
# mark commits as "squash" (s) in the editor, keep the first as "pick"
git push --force-with-lease
```

### Fixing the last commit
```bash
git add forgotten-file.txt
git commit --amend --no-edit
git push --force-with-lease
```

---

## Common Git Errors & Fixes

| Error | Fix |
|---|---|
| `fatal: not a git repository` | Run `git init` or `cd` into the correct folder |
| Merge conflict | Resolve markers manually, then `git add` + `git commit` |
| `Updates were rejected because the remote contains work...` | `git pull` (or `git pull --rebase`) before pushing |
| Accidentally committed to wrong branch | `git reset HEAD~1` then switch branch and re-commit, or `git cherry-pick` the commit onto the correct branch |
| Need to undo a pushed commit safely | `git revert <commit-hash>` instead of `reset` |
| Large file accidentally committed | Remove from history with `git filter-repo` or `BFG Repo-Cleaner`, then force-push |
| Detached HEAD state | `git checkout -b new-branch-name` to save your work into a branch |

---

*This README serves as a quick reference guide for essential Git commands, useful for daily development workflow and interview prep.*
