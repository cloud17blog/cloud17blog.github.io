---
layout: post
title:  "Git Hints and Tips"
date:   2024-04-01 20:37:53 +0100
categories: blog
---

## Git Hints and Tips

![Git Logo](../../../../../assets/images/blog/2024/04/01/git-hints-and-tips/git-banner.png)

## Introduction

Git has become a crucial tool for most developers to have at least basic familiarity with, and it's relatively easy to pick up the basics. However, there are a number of more advanced features I've learned over time, so I've listed many of them here for reference.

## Absolute Basics

When using git with a remote repository, you will typically have a local repository on your machine, and a remote repository on a server somewhere. The remote repository is usually on a service like GitHub, GitLab or Bitbucket, and you will interact with it using the git command line tool.

Work files are stored in the local repository, and you can push changes (edits, new files, deletes) to the remote repository, pull changes from it, and merge changes between versions (usually branches).

Files exist in four states:

- Untracked: Local files that are not yet being tracked by git
- Staged: Files that have been added to the staging area, ready to be committed
- Committed: Files that have been committed to the local repository
- Pushed: Committed files that have been pushed to the remote repository

## Basic Commands and Workflow

Below are the most basic commands you'll need to get started with Git, in approximately the order you are likely to use them:

Initialise a new repository:

```bash
# Create a new repository in the current directory:
git init
```

Set your user details either globally or just for the local repository:

```bash
# Set user details globally:
git config --global user.name "Your Name"
git config --global user.email "Your Email"

# Set user details for the local repository:
git config user.name "Your Name"
git config user.email "Your Email"
```

Verify your configuration:

```bash
# Show all configuration settings:
git config --list

# Show just the user settings:
git config user.name
git config user.email
```


Alternatively, "download" a remote repository to your local machine:

```bash
# Clone a remote repository to your local machine:
git clone <clone URL>
```

Get latest files from the remote repository:

```bash
# Just get the latest files for any existing local branches, optionally specifying a single branch:
git fetch [origin branch-name]

# Get the latest files for all remote branches:
git fetch --all

# Get latest files and merge them with local changes, optionally specifying a single branch:
git pull [origin branch-name]
```

Adding a file to the staging area:

```bash
# Add single file:
git add filename

# Add all files in directory and subdirectories:
git add .
```

Commit your changes to the local repository:

```bash
# Commit all changes with a message:
git commit -m "Commit message"
```

Push your changes to the remote repository:

If this was a new branch, you will need to give the remote branch a name too, usually the same as your local branch:

```bash
# Push changes to the remote repository:
git push --set-upstream origin branch-name
```

Otherwise a simple push is fine:

```bash
# Push changes to the remote repository:
git push
```

## Reverting Changes

If you've made a mistake, you can revert changes in a number of ways:

```bash
# Restore a work file from the staged version of it (which will be last committed file if no new commits done):
git restore <file>

# Restore version in staged from last commit:
git restore --staged <file>

# Set ALL staged files back to last commit (but do not reset work files):
git reset HEAD   # or git reset --mixed

# Set staged files AND work files back to last commit (all changes since last commit will be lost!):
git reset --hard

# "Undo" last n commit(s) by moving head (i.e. current point in the commit sequence) back n commits - do not update staging:
git reset HEAD~n --soft

# "Undo" last n commit(s) by moving head back n commits - update staging but not working:
git reset HEAD~n --mixed

# "Undo" last n commit(s) by moving head back n commits - update staging AND working:
git reset HEAD~n --hard
```

## Branching

Create a new branch and switch to it in one command:

```bash
git checkout -b new-branch-name
```

Delete a branch locally (and, optionally, remotely):

```bash
# Delete locally
git branch -d branch-name

# Delete remotely
git push origin --delete branch-name
```

Note you cannot be in the branch when you delete it, so may need to switch to another branch first:

```bash
# Switch to "main"" branch
git checkout main
```

