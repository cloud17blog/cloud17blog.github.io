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

## Renaming a Commit:

If you need to rename a commit message, you can do so with the following command:

```bash
git commit --amend -m "New commit message"
```

If you had already pushed the commit to the remote repository, you will need to force push the changes:

```bash
# Push the renamed commit and potentially overwrite yours or other users' 
# changes in the remote branch:
git push --force

# Push the renamed commit unless there are remote changes to merge locally first:
git push --force-with-lease
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

## Merging and Rebasing

### Option 1 - Using ```git merge```

This merges all the commits from the source branch into the target branch, keeping the commit history and chronological record of the changes made in both branches:

```bash
# Switch to the branch you want to merge changes into, e.g. main branch:
git checkout target-branch

# Merge changes from another branch into the current branch:
git merge source-branch
```

### Option 2 - Using ```git rebase```

Git rebase is an alternative to git merge, and is used to reapply commits from one branch onto another branch. This can be useful if you want to keep a linear commit history, rather than a branching one.

However this method of rewriting history can cause problems if the branch is shared with others, as it changes the commit history and introduces the potential for conflicts during the rebase process, which need to be resolved.

```bash
# Switch to the branch you want to rebase changes into, e.g. main branch:
git checkout target-branch

# Rebase changes from another branch into the current branch:
git rebase source-branch
```

To make the rebase process easier, you can use the ```--interactive``` flag to *squash* commits together, or reorder them:

```bash
# Rebase changes from another branch into the current branch, interactively:
git rebase --interactive source-branch
```

### Example of interactive rebase

Say we have three commits in ```branch2``` that we want to rebase onto ```branch1```. We can view the recent changes using the ```git log``` command:

```bash
# List the recent commits that are to be rebased:
git log --oneline

772984e (HEAD -> branch2) Change 3
a7ae81e Change 2
31c8a41 Change 1
a7173ba (main, branch1) Added Git Hints and Tips page initial version
```

We can then squash the last three commits into one, by running the following command:

```bash
# Rebase last 3 commits interactively (by viewing the last 4 commits):
git rebase --interactive HEAD~4
```

This will open a text editor with the last 3 commits listed, and you can change the word "pick" to "squash" for the commits you want to squash together:

```bash
pick a7173ba Added Git Hints and Tips page initial version
pick 31c8a41 Change To Keep
pick a7ae81e Change 2
pick 772984e Change 3

Rebase bc63f33..772984e onto bc63f33 (4 commands)
```

Becomes:

```bash
pick a7173ba Added Git Hints and Tips page initial version
pick 31c8a41 Change To Keep
squash a7ae81e Change 2
squash 772984e Change 3

Rebase bc63f33..772984e onto bc63f33 (4 commands)
```

Save and close the editor, and the rebase will be performed.

You will also be prompted to enter a new commit message for the squashed commits:

```bash
# This is a combination of 3 commits.
# This is the 1st commit message:

Change To Keep

# This is the commit message #2:

Change 2

# This is the commit message #3:

Change 3

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
```

Enter a new commit message for the first commit (i.e. the one representing the new merge) and remove any now superfluous messages. Save and close the editor, and the rebase will be completed.
