---
layout: post
title:  "Bash Command Hints and Tips: File Handling"
date:   2024-04-15 20:32:21 +0100
modified_date: 2024-04-15 20:32:21 +0100
categories: blog hints-and-tips
---

## Contents

- [Introduction](#introduction)
- [File Handling](#file-handling)
  - [Listing Files](#listing-files)
  - [Viewing the Contents of a File](#viewing-the-contents-of-a-file)
  - [Find Files](#find-files)
  - [Find Files and Execute Commands On Them](#find-files-and-execute-commands-on-them)
  - [Searching for Text in Files](#searching-for-text-in-files)
  - [View File Information](#view-file-information)
  - [Extracting Archive Files](#extracting-archive-files)
  - [Creating New Files](#creating-new-files)
  - [Creating New Files from Command Output](#creating-new-files-from-command-output)
  - [Copying Files](#copying-files)
  - [Moving and Renaming Files](#moving-and-renaming-files)
  - [Deleting Files](#deleting-files)

## Introduction

Following on from my [previous post](../../../../hints-and-tips/2024/04/15/bash-hints-and-tips.html) on Bash hints and tips, here are some more useful commands for file handling in Bash, including listing files, viewing file contents, finding files, searching for text in files, and more.

## File Handling

### Listing Files

Every Bash user will have used the `ls` command to list files in a directory. Here are a few useful variations on this command.

```bash
# List all files in the current directory
ls

# List files in the current directory showing filenames only in a vertical list - use number 1 not letter l
ls -1

# List files in a specific directory
ls /path/to/directory

# List files matching a specific pattern
ls *.txt

# List files in a specific directory matching a specific pattern
ls /path/to/directory/*.txt

# List all files in the current directory with detailed information
ls -l

# Or on many systems, a double l:
ll

# List all files in the current directory with detailed information and hidden files
ls -la

# List all files in the current directory with detailed information sorted by newest first
ls -lt
```

### Viewing the Contents of a File

There are a number of quick and easy ways to view the contents of a file in Bash:

```bash
# View the contents of a file
cat filename.txt

# View the contents of a file with line numbers
cat -n filename.txt

# Note cat is actually the command to concatenate files together, but as it outputs to
# the standard output, it is often used to view the contents of a file
cat file1.txt file2.txt

cat file1.txt file2.txt > combined.txt

# View the first e.g. 10 lines of a file
head -n 10 filename.txt

# View the last e.g. 10 lines of a file
tail -n 10 filename.txt

# View the contents of a file while it is being updated with "follow" option 
# VERY useful for log files!
tail -f filename.txt

# View a file page by page: 
# use enter to move forward line by line, space to move forward page by page, q to quit
more filename.txt

# Piping the output of a command to more, e.g. to view a long list of files:
ls -l | more

# View a file page by page - newer method:
# use enter to move forward line by line, space to move forward page by page, q to quit
# and arrow keys to freely move up and down
less filename.txt
```

### Find Files

To find files in a directory, you can use the `find` command. This command can be used to search for
files by name, size, type, and more.

```bash
# Find all files in the current directory
find .

# Find all files in the current directory and subdirectories
find . -type f

# Find all files in the current directory and subdirectories with a specific name
find . -type f -name "filename.txt"

# Find all files in the current directory and subdirectories with a specific extension
find . -type f -name "*.txt"

# Find all files in the current directory and subdirectories with a specific size
find . -type f -size +1M
```

### Find Files and Execute Commands On Them

To find files and execute commands on them, you can use the `find` command with the `-exec` option.

The files are denoted by `{}` and the command to execute is terminated with `\;`:

```bash
# Find all files in the current directory and subdirectories with a specific name and delete them
find . -type f -name "filename.txt" -exec rm {} \;

# Find all files in the current directory and subdirectories with a specific extension and copy them to a new location
find . -type f -name "*.txt" -exec cp {} /path/to/new-location \;
```

### Searching for Text in Files

To search for text in files, you can use the `grep` command. This command can be used to search for text in files by line, word, or pattern.

```bash
# Search for text in a file
grep "text" filename.txt

# Search for text in a file with line numbers
grep -n "text" filename.txt

# Search for text in a file with case-insensitive matching
grep -i "text" filename.txt

# Search for text in multiple files matching a specific filename pattern
grep "text" path/to/files/*.txt

# Display lines After the matched line - 
grep -A 5 "text" filename.txt

# Display lines Before the matched line
grep -B 5 "text" filename.txt

# Display lines before and after the matched line
grep -C 5 "text" filename.txt

# Display only the matched text
grep -o "text" filename.txt

# Display only lines not matching the text
grep -v "text" filename.txt

# Display only the count of matched lines
grep -c "text" filename.txt

# Display only the filenames of files containing the text
grep -l "text" path/to/files/*.txt

# Search all files in current folder, including hidden files:
grep "text" .

# Search all files in current folder and subfolders, including hidden files:
grep -r "text" .

# Search all files in current folder and subfolders, excluding hidden files:
grep -r "text" *
```

### View File Information

To view information about a file, you can use the `stat` command. This command can be used to view file permissions, size, and more.

```bash
# View information about a file
stat filename.txt
```

### Extracting Archive Files

To extract archive files, you can use the `tar` command. This is often useful if you need to "install" an application from a tarball
when you have not been able to find a valid package for your system.

```bash
# Extract files from a tar archive

tar -xvf archive.tar
```

### Creating New Files

To create a new file you can use the `touch` command. This can be used to create a new file or update the timestamp of an existing file.

```bash
# Create a new file
touch new-filename.txt

# Update the timestamp of an existing file
touch existing-filename.txt
```

### Creating New Files from Command Output

To create a file from the output of another command, you can use the `>` operator:

```bash
# Create a new file with the output of a command
echo "My test text" > new-filename.txt

# Appending to a file
echo "More text" >> existing-filename.txt

# Create a new file with the output of a command, e.g. list of files in the current directory
ls -1 > file-list.txt
```

### Copying Files

To copy files from one location to another, you can use the `cp` command.

```bash
# Copy a file to a new location
cp filename.txt /path/to/new-location

# Copy a directory to a new location
cp -r directory /path/to/new-location
```

### Moving and Renaming Files

To move files from one location to another, you can use the `mv` command. This command can also be used to rename files and directories.

```bash
# Move a file to a new location
mv filename.txt /path/to/new-location

# Move a directory to a new location
mv directory /path/to/new-location

# Rename a file
mv old-filename.txt new-filename.txt

# Rename a directory
mv old-directory new-directory
```

### Deleting Files

To delete files, you can use the `rm` command.

```bash
# Delete a file
rm filename.txt

# Delete a directory
rm -r directory

# Delete a file with confirmation
rm -i filename.txt
```
