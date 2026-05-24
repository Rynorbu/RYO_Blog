---
title: "Linux Simple Commands"
published: 2026-03-06
description: "A comprehensive guide to essential Linux terminal commands for everyday use."
# image: "../../assets/images/my_photo.jpg"
tags: ["Linux", "Commands", "DevOps", "Terminal", "Bash", "DSO101"]
category: DevOps
draft: false
---

## Introduction

Linux simple commands are the basic building blocks of working in the terminal. They help you move through folders, manage files, inspect text, search data, and control simple system tasks without using a graphical interface.

---

## Terminal Basics

### What is the Terminal?

The Linux terminal is a text-based interface where you type commands and get results immediately. It is one of the most powerful parts of Linux because it allows faster control, scripting, and automation than clicking through menus.

### Command Structure

A command usually follows this pattern:

```
command [options] [arguments]
```

- **Command** — Tells Linux what to do
- **Options** — Change how the command behaves (usually start with `-` or `--`)
- **Arguments** — The files or targets being used

### Example

```bash
ls -l /home
```

- `ls` = List command
- `-l` = Option for long format
- `/home` = Argument (the directory to list)

Result: "List files in long format inside /home"

---

## Navigation Commands

### Basic Navigation

The most common commands for moving around the filesystem are `pwd`, `ls`, and `cd`.

| Command | Purpose |
|---------|---------|
| `pwd` | Show current working directory |
| `ls` | List files and folders |
| `cd` | Change to another directory |

### PWD — Print Working Directory

Shows the current working directory, which tells you exactly where you are.

```bash
$ pwd
/home/user/documents
```

### LS — List Directory Contents

Lists files and folders in the current directory.

```bash
# Basic listing
ls

# Useful options
ls -a        # Show hidden files (starting with .)
ls -l        # Show detailed information (permissions, owner, size, date)
ls -lh       # List with human-readable file sizes
ls -la       # Combine -l and -a
ls -S        # Sort by file size
ls -t        # Sort by modification time
ls *.txt     # Show only .txt files
```

### CD — Change Directory

Changes the current directory to another folder.

```bash
# Navigate to home directory
cd ~
cd /home/user

# Go up one level
cd ..

# Go to root directory
cd /

# Go to previous directory
cd -

# Go to home directory (shortcut)
cd
```

---

## File Creation and Management

### Core File Commands

Linux has very simple commands for creating and managing files and directories.

| Command | Purpose |
|---------|---------|
| `mkdir` | Create a new directory |
| `touch` | Create an empty file or update timestamp |
| `cp` | Copy a file or directory |
| `mv` | Move or rename a file |
| `rm` | Delete a file |
| `rmdir` | Remove an empty directory |

### MKDIR — Make Directory

Creates a new directory.

```bash
# Create a single directory
mkdir notes

# Create nested directories
mkdir -p path/to/nested/folder
```

### TOUCH — Create or Update File

Creates an empty file or updates its timestamp.

```bash
# Create a new empty file
touch file.txt

# Create multiple files
touch file1.txt file2.txt file3.txt

# Update the timestamp of an existing file
touch existing_file.txt
```

### CP — Copy

Copies a file or directory.

```bash
# Copy a file
cp source.txt destination.txt

# Copy to a directory
cp file.txt /path/to/destination/

# Copy a directory and its contents
cp -r source_folder/ destination_folder/

# Copy with verbose output
cp -v file.txt destination.txt
```

### MV — Move or Rename

Moves a file or renames it.

```bash
# Move a file to another directory
mv file.txt /path/to/destination/

# Rename a file
mv old_name.txt new_name.txt

# Move and rename
mv source_file.txt /path/to/destination/new_name.txt

# Move multiple files
mv file1.txt file2.txt /destination/
```

### RM — Remove

Deletes a file. **Be careful — deleted files usually do not go to a recycle bin!**

```bash
# Remove a single file
rm file.txt

# Remove multiple files
rm file1.txt file2.txt file3.txt

# Remove a directory and everything inside it
rm -r folder_name

# Remove with confirmation
rm -i file.txt

# Remove without confirmation (dangerous!)
rm -f file.txt
```

### RMDIR — Remove Directory

Removes an empty directory.

```bash
# Remove empty directory
rmdir empty_folder

# Remove only if empty (safe)
rmdir folder_name
```

### Practical Example

```bash
# Create a folder for assignments
mkdir notes

# Add a file using touch
touch day1.txt

# Copy it for backup
cp day1.txt day1_backup.txt

# Rename the original
mv day1.txt lesson1.txt

# Later, clean up old practice folders
rm -r notes
```

---

## Reading File Contents

### File Viewing Commands

A major part of Linux work is reading files from the terminal.

| Command | Purpose |
|---------|---------|
| `cat` | Display entire file contents |
| `more` | Show file one screen at a time |
| `less` | Like more, with scrolling |
| `head` | Show first lines of file |
| `tail` | Show last lines of file |

### CAT — Concatenate and Display

Displays the full contents of a file. Best for small files.

```bash
# Display a file
cat file.txt

# Display multiple files
cat file1.txt file2.txt

# Display with line numbers
cat -n file.txt

# Display with visible special characters
cat -A file.txt
```

### MORE — View One Screen at a Time

Shows one screen at a time, useful for longer files.

```bash
# View file one screen at a time
more large_file.txt

# Press spacebar to go to next screen
# Press 'q' to quit
```

### LESS — Better Scrolling

Similar to `more` but allows easier backward and forward scrolling.

```bash
# View with better navigation
less large_file.txt

# Commands while viewing:
# Space  = next page
# b      = previous page
# /word  = search
# q      = quit
```

### HEAD — Show Beginning

Shows the first lines of a file (default: 10 lines).

```bash
# Show first 10 lines
head file.txt

# Show first 20 lines
head -n 20 file.txt
head -20 file.txt

# Show first 5 lines
head -5 file.txt
```

### TAIL — Show End

Shows the last lines of a file.

```bash
# Show last 10 lines
tail file.txt

# Show last 20 lines
tail -n 20 file.txt
tail -20 file.txt

# Follow a file as it grows (useful for logs)
tail -f /var/log/syslog
```

### When to Use Each Command

```
For small files       → cat
For long files       → less (or more)
For log files        → tail -f
For first N lines    → head -n N
For last N lines     → tail -n N
```

---

## Searching and Text Processing

### Searching Text

Searching is one of the most useful Linux skills. The `grep` command is used to find words or patterns inside files.

| Command | Purpose |
|---------|---------|
| `grep` | Search for pattern in files |
| `sort` | Sort lines |
| `uniq` | Remove duplicate adjacent lines |
| `wc` | Count lines, words, characters |
| `cut` | Extract parts of each line |
| `tr` | Transform characters |

### GREP — Search Text

Searches for matching lines in files.

```bash
# Basic search
grep word file.txt

# Search case-insensitive
grep -i word file.txt

# Search recursively in all files
grep -r word .

# Show line numbers
grep -n word file.txt

# Count matching lines
grep -c word file.txt

# Invert match (show non-matching lines)
grep -v word file.txt

# Show lines before and after
grep -B 2 -A 2 word file.txt
```

### SORT — Sort Lines

Sorts lines in a file.

```bash
# Sort alphabetically
sort file.txt

# Sort numerically
sort -n file.txt

# Sort in reverse
sort -r file.txt

# Sort and remove duplicates
sort -u file.txt
```

### UNIQ — Remove Duplicates

Removes duplicate adjacent lines.

```bash
# Remove duplicate adjacent lines
uniq file.txt

# Show only duplicates
uniq -d file.txt

# Count duplicates
uniq -c file.txt
```

### WC — Word Count

Counts lines, words, and characters.

```bash
# Count lines, words, bytes
wc file.txt

# Count only lines
wc -l file.txt

# Count only words
wc -w file.txt

# Count only characters
wc -c file.txt
```

### CUT — Extract Columns

Extracts parts of each line.

```bash
# Extract first 5 characters
cut -c1-5 file.txt

# Extract specific columns
cut -f1,3 file.txt
```

### TR — Transform Characters

Transforms characters.

```bash
# Convert lowercase to uppercase
tr 'a-z' 'A-Z' < file.txt

# Remove characters
tr -d 'a-z' < file.txt
```

### Practical Examples

```bash
# Search for error in logs
grep error /var/log/syslog

# Find all Python files
grep -r "def " *.py

# Sort a list and remove duplicates
sort file.txt | uniq

# Count lines in a file
wc -l file.txt

# Convert to uppercase
cat file.txt | tr 'a-z' 'A-Z'
```

---

## System Information Commands

### Checking System Status

Some simple commands help you check system status and user details.

| Command | Purpose |
|---------|---------|
| `whoami` | Show current user |
| `uname` | Show kernel and system info |
| `date` | Display current date and time |
| `history` | Show previously typed commands |
| `clear` | Clean the terminal screen |
| `df` | Show disk usage |
| `free` | Show memory usage |

### WHOAMI — Current User

Shows the current user.

```bash
$ whoami
user
```

### UNAME — System Information

Shows kernel and system information.

```bash
# All system information
uname -a

# Kernel name only
uname -s

# Kernel release
uname -r

# Hardware platform
uname -m
```

### DATE — Current Date and Time

Displays the current date and time.

```bash
# Display current date and time
date

# Format custom output
date "+%Y-%m-%d %H:%M:%S"
```

### HISTORY — Command History

Shows previously typed commands.

```bash
# Show all history
history

# Show last 20 commands
history 20

# Execute a command from history
!50   # Run command #50
!ls   # Run last command starting with 'ls'
```

### CLEAR — Clear Screen

Cleans the terminal screen.

```bash
clear
```

### DF — Disk Usage

Shows disk usage in a readable format.

```bash
# Show disk usage
df

# Show in human-readable format
df -h

# Show inode usage
df -i
```

### FREE — Memory Usage

Shows memory usage.

```bash
# Show memory usage
free

# Show in human-readable format
free -h

# Show in megabytes
free -m
```

---

## Help Commands

### Learning from the System

Linux is designed so users can learn commands from the system itself.

| Command | Purpose |
|---------|---------|
| `man` | Open manual page |
| `--help` | Short explanation of options |
| `whatis` | One-line description |
| `whereis` | Find binary and manual files |

### MAN — Manual Pages

Opens the manual page for a command.

```bash
# Read manual for a command
man ls
man grep
man chmod

# Search within manual
/search_word

# Quit manual
q
```

### HELP FLAG — Quick Help

Gets a short explanation of available options.

```bash
# Quick help
ls --help
grep --help
cd --help
```

### WHATIS — Command Description

Gives a one-line description.

```bash
whatis ls
whatis grep
whatis pwd
```

### WHEREIS — Locate Files

Shows where the binary and manual files are located.

```bash
whereis ls
whereis python
```

---

## File Paths and Wildcards

### Understanding Paths

A **path** tells Linux where a file or folder is located.

| Type | Example | Meaning |
|------|---------|---------|
| **Absolute** | `/home/user/file.txt` | Full path from root |
| **Relative** | `file.txt` | File in current directory |
| **Relative** | `./folder/file.txt` | File in subdirectory |
| **Relative** | `../file.txt` | File in parent directory |

### Wildcards

Wildcards are special symbols used to match multiple files.

| Wildcard | Matches | Example |
|----------|---------|---------|
| `*` | Any number of characters | `*.txt` matches all text files |
| `?` | One character | `file?.txt` matches file1.txt, fileA.txt |
| `[abc]` | One of listed characters | `file[123].txt` matches file1.txt, file2.txt |
| `[a-z]` | Range of characters | `[a-z]*.txt` |

### Practical Examples

```bash
# List all text files
ls *.txt

# Remove files like file1.txt or fileA.txt
rm file?.txt

# Copy all JPG images into a folder
cp *.jpg images/

# Move all PDFs
mv *.pdf documents/

# List files starting with test
ls test*

# List all files and folders (including hidden)
ls -la
```

---

## Permissions and Ownership

### Understanding File Permissions

Even with simple commands, it is useful to know that Linux protects files using permissions. When you run `ls -l`, you can see whether a file is readable, writable, or executable.

### Viewing Permissions

```bash
$ ls -l
-rw-r--r-- 1 user group  1234 May 25 10:30 file.txt
│ └┬┘ └┬┘ └┬┘
│  │   │   └─ Others
│  │   └───── Group
│  └───────── Owner
└────────────── File type
```

### Permission Commands

| Command | Purpose |
|---------|---------|
| `chmod` | Change file permissions |
| `chown` | Change file owner |
| `chgrp` | Change file group |

### Impact of Permissions

If you do not have permission, the command may fail even if the file exists.

```bash
# If you get permission denied
Permission denied

# Change permissions to allow
chmod +x script.sh

# Change owner
chown user:group file.txt
```

---

## Process and Service Commands

### Managing Running Programs

Some simple Linux commands help you work with running programs and services.

| Command | Purpose |
|---------|---------|
| `ps` | Show running processes |
| `top` | Show live system activity |
| `kill` | Stop a process |
| `systemctl` | Control system services |

### PS — Process Status

Shows running processes.

```bash
# Show all processes
ps aux

# Show specific process
ps aux | grep python

# Show process tree
ps aux --forest
```

### TOP — System Activity

Shows live system activity and resource usage.

```bash
# Show live system activity
top

# Show specific process
top -p 1234
```

### KILL — Stop Process

Stops a process.

```bash
# Kill a process by PID
kill 1234

# Force kill
kill -9 1234
```

### SYSTEMCTL — Control Services

Controls system services.

```bash
# Start a service
sudo systemctl start service_name

# Stop a service
sudo systemctl stop service_name

# Check status
sudo systemctl status service_name
```

### Practical Use

```bash
# Find which program is using too much CPU
top

# Kill a frozen process
kill 1234

# Restart a service
sudo systemctl restart nginx
```

---

## Network Commands

### Basic Network Testing

A few beginner-friendly commands are useful for checking network connectivity.

| Command | Purpose |
|---------|---------|
| `ping` | Test system reachability |
| `traceroute` | Show path packets take |
| `netstat` / `ss` | Show open ports and connections |

### PING — Test Connectivity

Tests whether a system is reachable.

```bash
# Ping a host
ping google.com

# Ping with limited attempts
ping -c 4 google.com

# Ping with timeout
ping -w 5 google.com
```

### TRACEROUTE — Show Network Path

Shows the path packets take to reach a destination.

```bash
traceroute google.com
```

### SS or NETSTAT — Open Ports

Shows open ports and connections.

```bash
# Show all connections
ss -a

# Show listening ports
ss -l

# Show TCP connections
ss -t

# Show with process info
ss -tlnp
```

---

## Practice Sequence

### Hands-On Practice

Here is a simple practice sequence to understand how commands work together:

```bash
# 1. Show current directory
pwd

# 2. List all files including hidden
ls -la

# 3. Create a new directory
mkdir test

# 4. Change to the new directory
cd test

# 5. Create a file
touch sample.txt

# 6. Add content using echo
echo "Hello Linux" > sample.txt

# 7. Display the file content
cat sample.txt

# 8. Go back to parent directory
cd ..

# 9. Remove the test directory
rm -r test

# 10. Verify it's deleted
ls -la
```

### What You Learned

- Navigation (`pwd`, `cd`, `ls`)
- Directory creation (`mkdir`)
- File creation (`touch`)
- Redirecting output (`>`)
- Viewing files (`cat`)
- Cleaning up (`rm -r`)

---

## Common Mistakes

### Case Sensitivity

Linux is **case-sensitive**, so these are different files:

- `File.txt`
- `file.txt`
- `FILE.TXT`

### Dangerous Commands

Be careful with `rm` because deleted files usually do not go to a recycle bin!

```bash
# DANGEROUS - Removes everything!
rm -r /

# Safe alternative - use trash-cli
trash-put file.txt
```

### Missing Options

Some commands need options, not just file names:

```bash
# Wrong - won't work for folders
rm folder

# Correct - use -r for directories
rm -r folder

# Wrong - won't show hidden files
ls

# Correct - use -a to show hidden files
ls -a
```

### Forgetting Paths

```bash
# If file is in another directory, use full path
cat /home/user/documents/file.txt

# Not just the filename
cat file.txt   # May not work if not in current directory
```

---

## Quick Reference — Command Cheat Sheet

### Navigation and File Management

| Command | Purpose |
|---------|---------|
| `pwd` | Show current folder |
| `ls` | List files and folders |
| `cd` | Change folder |
| `mkdir` | Create directory |
| `touch` | Create empty file |
| `cp` | Copy files |
| `mv` | Move or rename files |
| `rm` | Remove files |
| `rmdir` | Remove empty directory |

### File Viewing

| Command | Purpose |
|---------|---------|
| `cat` | Show file content |
| `more` | View file one screen at a time |
| `less` | Better scrolling view |
| `head` | Show beginning of file |
| `tail` | Show end of file |

### Text Processing

| Command | Purpose |
|---------|---------|
| `grep` | Search text in files |
| `sort` | Sort lines |
| `uniq` | Remove duplicates |
| `wc` | Count lines, words, characters |
| `cut` | Extract columns |
| `tr` | Transform characters |

### System Information

| Command | Purpose |
|---------|---------|
| `whoami` | Show current user |
| `uname` | Show system info |
| `date` | Show current date/time |
| `history` | Show past commands |
| `clear` | Clear screen |
| `df` | Show disk usage |
| `free` | Show memory usage |

### Help and Documentation

| Command | Purpose |
|---------|---------|
| `man` | Read manual |
| `--help` | Quick help |
| `whatis` | Command description |
| `whereis` | Find binary location |

### Processes and Services

| Command | Purpose |
|---------|---------|
| `ps` | Show processes |
| `top` | Live system activity |
| `kill` | Stop process |
| `systemctl` | Control services |

### Network Commands

| Command | Purpose |
|---------|---------|
| `ping` | Test connectivity |
| `traceroute` | Show network path |
| `ss` | Show ports and connections |

---

## Summary

Linux simple commands become easier with practice, and once you understand them, you can use the terminal for work, study, and troubleshooting much more confidently.

### Key Takeaways

- **Terminal is powerful** — Faster than GUI for many tasks
- **Commands follow a pattern** — `command [options] [arguments]`
- **Practice regularly** — Type commands yourself to build muscle memory
- **Use help** — `man` and `--help` are always available
- **Be careful with dangerous commands** — Especially `rm`
- **Learn gradually** — Master basics before advanced topics

### Next Steps

1. Open a terminal
2. Practice each command category
3. Use `man` when you need details
4. Combine commands to solve real problems
5. Master scripting to automate tasks
