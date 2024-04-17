---
layout: post
title:  "Bash Command Hints and Tips"
date:   2024-04-15 20:32:21 +0100
categories: blog hints-and-tips
---

## Introduction

Many Bash users who started out with Windows or Mac systems will probably have absorbed the their
basic scripting knowledge by repeatedly using the same basic commands over and over again and
without fully realising range of commands available to them.

This post is a collection of some of the more interesting and useful commands I've come across.

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

## Network Commands

### Checking Network Connectivity

To check if a host is reachable on the network, you can use the `ping` command.

```bash
# Ping a host
ping hostname.com
```

However, this uses a specific protocol (ICMP) which may not be enabled, or may be blocked by firewalls.

To check if a host is reachable on a specific port, you can use the `telnet` or, if this is not installed,
as is often the case on very minimal install servers, the `netcat` (often just `nc`) command instead.

```bash
# Check if a host is reachable on a specific port using telnet
telnet hostname.com 80

# Check if a host is reachable on a specific port using nc
nc -zv hostname.com 80
```

### Checking Network Ports

To check which ports are open on a host, you can use the `nmap` command.

```bash
# Check which ports are open on a host
nmap hostname.com

# Check which ports are open on a host with a specific range of ports
nmap -p 1-100 hostname.com

# Example output
# Starting Nmap 7.80 ( https://nmap.org ) at 2024-04-15 20:32 BST
# Nmap scan report for hostname.com (
# Host is up (0.00012s latency).
# Not shown: 99 closed ports
# PORT   STATE SERVICE
# 80/tcp open  http
# 443/tcp open  https
#
# Nmap done: 1 IP address (1 host up) scanned in 0.08 seconds

```

### Checking routes to a host

To check the route to a host, you can use the `traceroute` command.

```bash
# Check the route to a host
traceroute hostname.com
```

### Look up DNS information

To look up DNS information for a host, you can use the `nslookup` or the `dig` commands.

```bash
# Look up DNS information for a host
nslookup hostname.com

# Look up DNS information for a host using dig
dig hostname.com

# Look up DNS information for a host using dig with a specific record type
dig hostname.com CNAME

```

## Command History

### Viewing Command History

To view a list of your recently used commands, you can use `history`:

```bash
# View the command history
history

# View a specific number of commands from the history
history 10
```

### Repeating Commands

To repeat a command from the history, you can use the `!` operator followed by the command number:

```bash
# Execute a specific command from the history:
# Use the number from the history list with an exclamation mark in front of it:
!10
```

### Searching Command History

To search for a specific command in the history, you can use the `Ctrl+R` shortcut from the terminal
prompt, and then start typing the command you are looking for.

However, an easier way to search for a specific command in the history is to use the `history | grep` command:

```bash
# Search for a specific command in the history
history | grep "command"
```

### Setting Command History Size

The history is usually stored in usually `~/.bash_history`, and the size of this is controlled by two environment variables: `HISTSIZE` and `HISTFILESIZE`.

You can (and probably should!!) increase these values by changing the following lines in your `~/.bashrc` file:

```bash
export HISTSIZE=5000
export HISTFILESIZE=10000
```

This will set the history size for the current session to 5000 commands and the history file size to 10000 commands.

After modifying `~/.bashrc`, you need to apply the changes to the current session using:

```bash
source ~/.bashrc
```

Or you can restart your terminal session.

## Script Control

### Running Scripts

To run a script in Bash, you can use the `./` operator followed by the script name:

```bash
# Run a script
./myscript.sh
```

For scripts that you intend to run frequently, it is a good idea to:

- Save them without the `.sh` extension so you don't have to type it every time
- Place them in a directory that is in your `PATH` environment variable, such as
`/usr/bin`, `/usr/local/bin` or `~/bin`
- Make them executable by using the `chmod` command:

```bash
# Make a script executable
chmod +x myscript.sh
```

You can then run this script as if it were a built-in command:

```bash
# Run a script as a command
myscript
```

### Declaring an Alias

To create an alias for a command or script, you can use the `alias` command:

```bash
# Create an alias for a command
alias ll='ls -la'

# Create an alias for a script
alias myscript='~/path/to/some-script.sh'

# Pass parameters to an alias
alias myscript='~/path/to/some-script.sh $1 $2'

# Run an alias and pass parameters to it:
myscript arg1 arg2
```

Aliases are only available for the current session, so to make them permanent, you can add them to your `~/.bashrc` file:

```bash
# Add an alias to the ~/.bashrc file
echo "alias ll='ls -la'" >> ~/.bashrc

# Apply the changes to the current session
source ~/.bashrc
```

### Remove an alias

```bash
unalias ll
```

### Changing Directory in a Script

To change the directory in a script, you can use the `cd` command:

```bash
!#/bin/bash

# Change the directory in a script
cd /path/to/directory

# Do something in the new directory
ls
```

However, this path change only applies to the script itself and not the terminal
session that runs the script.

If you wish to use a script to change the directory in the terminal session, you
can use the `.` operator when executing it:

```bash
# Run a script to change the directory in the terminal session
. myscript.sh
```

However for this particular task, it is probably better to use an alias as described above.

### Switching Between Two Directories from the Command Line

To switch between two directories from the command line, you can use the `cd -` command:

```bash
# Switch between two directories

# Change to directory1
cd /path/to/directory1

# Change to directory2
cd /path/to/directory2

# Switch back to directory1
cd -
```

### Running Scripts in the Background

To run a script in the background, you can use the `&` operator:

```bash
# Run a script in the background
./myscript.sh &

# Stop a script that is running in the background
kill PID
```

You can also send an existing process to the background by pressing **Ctrl+Z** to pause the
process, then typing **bg** to send it to the background.
{:.text-box}

### Running Scripts with Arguments

To run a script with arguments, you can pass the arguments after the script name and read them in the script by position number:

```bash
# Run a script with arguments
./myscript.sh arg1 arg2

# Read the arguments in the script file:

!#/bin/bash
arg1=$1
arg2=$2

echo "Argument 1: $arg1"
echo "Argument 2: $arg2"

```

### Verbose Script Output

To output verbose information from a script, you can use the `set -x` command at the start of the script:

```bash
# Output verbose information from a script
set -x

# Do something in the script
...
```

When this script runs, it will output each command that is executed, which can be useful for debugging.

## To Do

- Add ~/.bashrc commands to show git branch in terminal
- Add more commands and examples
- Add example output for each command

Thanks to friend and colleague Alastair for showing me many of these commands!
{:.text-box}
