---
layout: post
title:  "Bash Command Hints and Tips"
date:   2024-04-15 20:32:21 +0100
modified_date: 2024-04-15 20:32:21 +0100
categories: blog hints-and-tips
---

## Contents

- [Introduction](#introduction)
- [String Expansions](#string-expansions)
  - [Substrings](#substrings)
  - [String Substitution](#string-substitution)
  - [Case Conversion](#case-conversion)
  - [String Length](#string-length)
  - [Variable Expansion](#variable-expansion)
  - [Checking for null or empty strings](#checking-for-null-or-empty-strings)
  - [Checking for non-null or non-empty strings](#checking-for-non-null-or-non-empty-strings)
  - [Default values](#default-values)
  - [Read into a string variable from a file](#read-into-a-string-variable-from-a-file)
  - [Read into a string variable from a command](#read-into-a-string-variable-from-a-command)
- [Command History](#command-history)
  - [Viewing Command History](#viewing-command-history)
  - [Repeating Commands](#repeating-commands)
  - [Searching Command History](#searching-command-history)
  - [Setting Command History Size](#setting-command-history-size)
- [Script Control](#script-control)
  - [Running Scripts](#running-scripts)
  - [Declaring an Alias](#declaring-an-alias)
  - [View Existing Alias Definitions](#view-existing-alias-definitions)
  - [Remove an alias](#remove-an-alias)
  - [Changing Directory in a Script](#changing-directory-in-a-script)
  - [Switching Between Two Directories from the Command Line](#switching-between-two-directories-from-the-command-line)
  - [Running Scripts in the Background](#running-scripts-in-the-background)
  - [Running Scripts with Arguments](#running-scripts-with-arguments)
  - [Verbose Script Output](#verbose-script-output)
- [Network Commands](#network-commands)
  - [Checking Network Connectivity](#checking-network-connectivity)
  - [Checking Network Ports](#checking-network-ports)
  - [Checking routes to a host](#checking-routes-to-a-host)
  - [Looking up DNS information](#looking-up-dns-information)
  
## Introduction

Many Bash users who started out with Windows or Mac systems will probably have absorbed the their
basic scripting knowledge by repeatedly using the same basic commands over and over again and
without fully realising range of commands available to them.

This post is a collection of some of the more interesting and useful commands I've come across.

## String Expansions

There are nearly unlimited ways to manipulate strings in Bash, but here are a few of the most common:

### Substrings

To extract a substring from a string, you can use the `${string:position:length}` syntax:

```bash
string="Hello, World!"

# Extract a substring from the start of a string
substring=${string: 5}

echo "Substring: $substring"
> Substring: Hello

# Extract a substring from the middle of a string
substring=${string:7:5}

echo "Substring: $substring"
> Substring: World

# Extract a substring from the end of a string
substring=${string: -6}

echo "Substring: $substring"
> Substring: World!
```

### String Substitution

To substitute a substring in a string, you can use the `${string/pattern/replacement}` syntax:

```bash
string="Hello, World!"

# Substitute a substring in a string
newstring=${string/World/Universe}

echo "New String: $newstring"
> New String: Hello, Universe!
```

### Case Conversion

To convert a string to uppercase or lowercase, you can use the `${string^^}` or `${string,,}` syntax:

```bash

string="Hello, World!"

# Convert a string to uppercase
uppercase=${string^^}

echo "Uppercase: $uppercase"
> Uppercase: HELLO, WORLD!

# Convert a string to lowercase
lowercase=${string,,}

echo "Lowercase: $lowercase"
> Lowercase: hello, world!
```

### String Length

To get the length of a string, you can use the `${#string}` syntax:

```bash
string="Hello, World!"

# Get the length of a string
length=${#string}

echo "Length: $length"
> Length: 13

# Trim the string to a specific length and add a suffix if it is longer than a specific length
max_length=10
suffix="..."
trimmed_string=${string:0:max_length}${#string > max_length && echo $suffix}

echo "Trimmed String: $trimmed_string"
> Trimmed String: Hello, Wor...
```

### Variable Expansion

To expand a variable in a string, you can use the `${variable}` syntax to ensure the variable is expanded correctly:

Rather than this:

```bash
variable="World"

# Expand a variable in a string
echo "Hello, $variable!"
> Hello, World!
```

... do this, which will protect you from unexpected behaviour:

```bash
variable="World"

# Expand a variable in a string
echo "Hello, ${variable}!"
> Hello, World!
```

### Checking for null or empty strings

To check if a variable is null or empty, you can use the `-z` option:

```bash
# Check if a variable is null or empty
if [ -z "$variable" ]; then
    echo "Variable is null or empty"
fi
```

### Checking for non-null or non-empty strings

To check if a variable is not null or empty, you can use the `-n` option:

```bash
# Check if a variable is not null or empty
if [ -n "$variable" ]; then
    echo "Variable is not null or empty"
fi
```

### Default values

To set a default value for a variable if it is null or empty, you can use the `${variable:-default}` syntax:

```bash
# Set a default value for a variable if it is null or empty
input="a"
default="b"
result=${input:-$default}

echo "Result: $result"
```

### Read into a string variable from a file

To read the contents of a file into a string variable, you can use the `cat` command:

```bash
# Read the contents of a file into a string variable
file="filename.txt"
string=$(cat $file)

echo "Contents: $string"
```

Or, use output redirection:

```bash
# Read the contents of a file into a string variable
file="filename.txt"
string=$(< $file)

# read the contents of multiple files into a string variable
string=$(< file1.txt file2.txt)

echo "Contents: $string"
```

### Read into a string variable from a command

To read the output of a command into a string variable, you can use the `$(command)` syntax:

```bash
# Read the output of a command into a string variable
current_date=$(date)
echo "Current date: $current_date"

port_info=$(nmap -p 1-100 hostname.com)
echo "Port Information: $port_info"

> Port Information: Starting Nmap 7.80 ( https://nmap.org ) at 2024-05-11 21:01 BST
Stats: 0:00:00 elapsed; 0 hosts completed (0 up), 1 undergoing Ping Scan
...
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

1. Save them without the `.sh` extension so you don't have to type it every time
1. Place them in a directory that is in your `PATH` environment variable, such as
`/usr/bin`, `/usr/local/bin` or `~/bin`
1. Make them executable by using the `chmod` command:

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

Aliases are only available for the current session, so to make them permanent, you can add them to your `~/.bashrc` file either by editing it directly, or by using the following commands:

```bash
# Add an alias to the ~/.bashrc file
echo "alias ll='ls -la'" >> ~/.bashrc

# Apply the changes to the current session
source ~/.bashrc
```

### View Existing Alias Definitions

To view a list of existing aliases, you can simply use the `alias` command without any further parameters:

```bash
# View a list of existing aliases
alias
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

You can also use the `$@` variable to read all arguments passed to the script:

```bash
# Read all arguments in the script file:

!#/bin/bash
args=$@

echo "Arguments: $args"
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

### Looking up DNS information

To look up DNS information for a host, you can use the `nslookup` or the `dig` commands.

```bash
# Look up DNS information for a host
nslookup hostname.com

# Look up DNS information for a host using dig
dig hostname.com

# Look up DNS information for a host using dig with a specific record type
dig hostname.com CNAME

```

## To Do

- Add ~/.bashrc commands to show git branch in terminal
- Add more commands and examples
- Add example output for each command

Thanks to friend and colleague Alastair for showing me many of these commands!
{:.text-box}
