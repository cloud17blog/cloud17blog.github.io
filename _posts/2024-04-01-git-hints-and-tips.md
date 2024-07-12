---
layout: post
title:  "Git Hints and Tips"
date:   2024-04-01 20:37:53 +0100
categories: blog hints-and-tips
---

![Git Logo](../../../../../assets/images/blog/2024/04/01/git-hints-and-tips/git-banner.png)

## Contents

- [Introduction](#introduction)
- [Absolute Basics](#absolute-basics)
- [Basic Commands and Workflow](#basic-commands-and-workflow)
- [Branching](#branching)
- [Fixing Mistakes](#fixing-mistakes)
  - [Changing a Commit Message](#changing-a-commit-message)
  - [Adding a Missing File to a Commit](#adding-a-missing-file-to-a-commit)
  - [Reverting Changes](#reverting-changes)
  - [Committed to the Wrong Branch](#committed-to-the-wrong-branch)
  - [Renaming a Branch](#renaming-a-branch)
  - [Pushing Changes to the Remote Repository](#pushing-changes-to-the-remote-repository)
- [Merging and Rebasing](#merging-and-rebasing)
  - [Option 1 - Using ```git merge```](#option-1---using-git-merge)
  - [Option 2 - Using ```git rebase```](#option-2---using-git-rebase)
  - [Example of Interactive Rebase](#example-of-interactive-rebase)

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

Alternatively, if the code you wish to work with already exists in a remote repository, "download" to your local machine:

```bash
# Clone a remote repository to your local machine:
git clone <clone URL>
```

Get latest files from the remote repository:

```bash
# Just get the latest files for any existing local branches, optionally specifying a single branch:
git fetch [origin <branch-name>]

# Get the latest files for all remote branches:
git fetch --all

# Get latest files and merge them with local changes, optionally specifying a single branch:
git pull [origin <branch-name>]
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
git push --set-upstream origin <branch-name>
```

Otherwise a simple push is fine:

```bash
# Push changes to the remote repository:
git push
```

## Branching

Create a new branch and switch to it in one command:

```bash
git checkout -b <new-branch-name>
```

Delete a branch locally (and, optionally, remotely):

```bash
# Delete locally
git branch -d <branch-name>

# Delete remotely
git push origin --delete <branch-name>
```

Note you cannot be in the branch when you delete it, so may need to switch to another branch first:

```bash
# Switch to "main"" branch
git checkout main
```

## Fixing Mistakes

### Changing a Commit Message

If you need to rename a commit message, you can do so with the following command:

```bash
git commit --amend -m "New commit message"
```

### Adding a Missing File to a Commit

If you forgot to add a file to the staging area before committing, you can add it and then amend the commit:

```bash
# Add the missing file to the staging area:
git add filename

# Amend the last commit with the new file:
git commit --amend
```

### Reverting Changes

If you've made a mistake, you can revert changes in a number of ways:

```bash
# Unstage a file that was added to the staging area:
git reset <file>

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

### Committed to the Wrong Branch

If you've committed to the wrong branch (e.g. main/master), you can "move" the commit to the correct branch by creating the intended new branch and resetting the original back to its prior commit state:

```bash
# Create a new branch from the current branch, but stay in the original branch:
git branch <new-branch-name>

# Reset the original branch back to the commit before (HEAD~1 means one commit back):
git reset HEAD~1 --hard

# Switch to the new branch, which still contains the commit:
git checkout <new-branch-name>
```

### Renaming a Branch

If you need to rename a branch, you can do so with the following command:

```bash
# Rename the current branch by moving it to a new name:
git branch -m <new-branch-name>
```

Easy!

### Pushing Changes to the Remote Repository

If you had already pushed the commit that you are fixing to the remote repository, you will need to "force push" the new changes:

```bash
# Push the renamed commit and potentially overwrite yours or other users' 
# changes in the remote branch:
git push --force

# Push the renamed commit unless there are remote changes to merge locally first:
git push --force-with-lease
```

## Comparing Branches and Commits

To compare the current edited files with the last commit, you can use the ```git diff``` command:

```bash
# Compare the current edited files with the last commit:
git diff

# Compare the current edited files with the last commit for a specific file:
git diff <filename>
```

To compare branches, you can use the ```git diff``` command with the branch names as arguments:

```bash
# Compare the changes between two branches:
git diff <branch-name-1>..<branch-name-2>
```

To compare commits, you can use the ```git log``` command:

```bash
# Compare the changes between two commits:
git log <commit-hash-1>..<commit-hash-2>
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

### Example of Interactive Rebase

To make the rebase process easier, especially if merge conflicts need to be resolved, you can first rebase the branch to remove commits that do not need to appear in the final commit history.

To do this, you use the ```--interactive``` flag with the rebase command, which allows you to choose which commits to `pick` (keep), `squash` (merge the commits but keep all the commit messages), `fixup` (merge the commits and only keep the oldest commit message) or `drop`, and to reorder them as required.

#### Example

Let's say we have three commits in `branch2` that we want to rebase onto `branch1` as a single commit.

First, you can use the `git log` command to view the recent commits (hashes and comments) that are to be considered:

```bash
# List the recent commits that are to be rebased:
git log --oneline

d75ce0b (HEAD -> branch2) Change 3 in branch2
e735dc0 Change 2 in branch2
772984e Change 1 in branch2                                         # message to keep
ebbf84b (origin/main, branch1) Final commit message for branch1     # branch 2 created at this point
a3bf4c2 Made a commit to branch 1
```

Then you can initiate the rebase, specifying the number of commits to include. This is done using the ```HEAD~n``` syntax, where n is the number of commits to rebase:

```bash
# Rebase last 3 commits interactively (by viewing the last 4 commits):
git rebase --interactive HEAD~4
```

This will open a text editor with the last 4 commits listed, *oldest to newest*:

```bash
pick ebbf84b Final commit message for branch1
pick 772984e Change 1 in branch2
pick e735dc0 Change 2 in branch2
pick d75ce0b Change 3 in branch2

Rebase 772984e..d75ce0b onto 772984e (4 commands)
```

You can then change `pick` to `squash` (or simply `s`) or to `fixup` (`f`) for the commits you want to `squash` together (code is merged into a single commit but all commit messages are kept with it), or `fixup` (code is merged into a single commit and only the earliest commit message is kept), or to `drop` (`d`) for the commits you want to remove from the history:

```bash
pick ebbf84b Final commit message for branch1
pick 772984e Change 1 in branch2
f e735dc0 Change 2 in branch2
f d75ce0b Change 3 in branch2

Rebase 772984e..d75ce0b onto 772984e (4 commands)
```

Exit the editor and save the changes, and the rebase will be performed.

Now when we look at the commit history, we see that the three commits from `branch2` have been squashed into a single commit on `branch1`:

```bash
# List the recent commits after the rebase:
git log --oneline

772984e (HEAD -> branch2) Change 1 in branch2
ebbf84b (origin/main, branch1) Final commit message for branch1     # branch 2 created at this point
a3bf4c2 Made a commit to branch 1
```

It's a good habit to get into to always make the first commit message a plausible one to keep no matter how many other "work in progress" commit messages you subsequently add, as this will be the default message for the new commit that is created when the commits are squashed.

However, there are three other options you can use to change the commit message for the new commit that is created when the commits are squashed:

### During the Rebase

In the interactive rebase editor, you can change the word `pick` to one of the following:

- `reword` or `r` - to change the commit message
- `edit` or `e` - to change the commit message and/or the commit itself

After performing the rebase, you will be prompted to enter a new commit message for the squashed commits.

### After the Rebase

If you forget to change the commit message during the rebase, you can still change it afterwards using the `git commit --amend` command as described above.

```bash
# Rebase changes from another branch into the current branch, interactively:
git rebase --interactive source-branch
```


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

If required, edit the commit message for the first commit (i.e. the one representing the new merge) and remove any now superfluous messages.

Save and close the editor, and the rebase will be completed with the three commits from ```branch2``` merged as a new commit into ```branch1```.


Add:
git rebase main into local branch
git rebase drop commits

https://www.git-scm.com/download/linux
