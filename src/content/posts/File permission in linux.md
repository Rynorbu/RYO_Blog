---
title: "File Permissions in Linux"
published: 2026-03-06
description: "Core concepts about file permissions in Linux and how they work."
# image: "../../assets/images/my_photo.jpg"
tags: ["Linux", "File Permissions", "Security", "DevOps", "DSO101"]
category: DevOps
draft: false
---

## Introduction

Linux file permissions are one of the most important parts of Linux security. They control who can read, write, or execute a file or directory, and they help protect the system from accidental or unauthorized access.

## What File Permissions Mean

Every file and directory in Linux has three permission groups:

- **Owner** — The user who owns the file
- **Group** — A set of users who share access
- **Others** — Everyone else on the system

### Basic Permissions

Each of these groups can have three basic permissions:

- **Read (r)** — Allows viewing the contents of a file or listing a directory
- **Write (w)** — Allows modifying a file or creating/deleting items in a directory
- **Execute (x)** — Allows running a file as a program or entering a directory

## How Permissions Look

When you run `ls -l`, Linux shows permissions in a string such as `-rwxr-xr--`. The first character tells the file type, such as `-` for a normal file or `d` for a directory, and the next nine characters are divided into three sets of three for owner, group, and others.

### Example: `-rwxr-xr--`

- `-` = Regular file (not a directory)
- `rwx` = Owner can read, write, and execute
- `r-x` = Group can read and execute, but not write
- `r--` = Others can only read

### Breaking It Down

```
-rwxr-xr--
│└┬┘└┬┘└┬┘
│ │  │  └─── Others (r--)
│ │  └────── Group (r-x)
│ └───────── Owner (rwx)
└──────────── File Type (-)
```

## Directory Permissions

Directory permissions work a little differently from file permissions. A directory needs execute permission to be accessed or entered, and read permission to view the list of files inside it.

### What Each Permission Does on Directories

- **Without x (execute)** — You cannot `cd` into the directory
- **Without r (read)** — You cannot list its contents with `ls`
- **Without w (write)** — You cannot create, delete, or rename files inside it

### Why This Matters

Directory permissions are very important for controlling access to:

- Shared project folders
- System directories
- Home directories
- Sensitive locations

## Numeric Permission Values

Linux also represents permissions using numbers. These values are:

- `r` (read) = **4**
- `w` (write) = **2**
- `x` (execute) = **1**

These are added together to form a single digit for each group:

### Common Combinations

| Number | Permissions | Meaning |
|--------|-------------|---------|
| 7 | rwx | Read, Write, Execute |
| 6 | rw- | Read, Write |
| 5 | r-x | Read, Execute |
| 4 | r-- | Read Only |
| 3 | -wx | Write, Execute |
| 2 | -w- | Write Only |
| 1 | --x | Execute Only |
| 0 | --- | No Permissions |

### Examples

```bash
chmod 755 file
# Owner: rwx (7), Group: r-x (5), Others: r-x (5)

chmod 644 file
# Owner: rw- (6), Group: r-- (4), Others: r-- (4)

chmod 700 file
# Owner: rwx (7), Group: --- (0), Others: --- (0)
```

## Symbolic Permission Changes

You can change permissions with `chmod` using symbolic mode too. This is useful when you want to make a small, precise change without rewriting the whole permission set.

### Symbolic Mode Syntax

- `u` = user/owner
- `g` = group
- `o` = others
- `a` = all (everyone)

### Permission Operators

- `+` = Add permission
- `-` = Remove permission
- `=` = Set exact permission

### Examples

```bash
# Add execute permission for the owner
chmod u+x script.sh

# Remove write permission from group and others
chmod go-w file.txt

# Remove all permissions from everyone
chmod a-rwx secret.txt

# Add write permission for owner and group
chmod ug+w document.txt

# Set exact permissions (owner can read/write, others can read)
chmod u=rw,o=r file.txt
```

## Ownership and Groups

Permissions are connected to ownership. Each file has an owner and a group, and Linux checks whether the current user matches the owner, belongs to the group, or is treated as others.

### Changing Ownership

You usually change ownership with:

- **`chown`** — Changes the owner
- **`chgrp`** — Changes the group
- **`chown user:group file`** — Changes both owner and group at once

### Examples

```bash
# Change owner to alice
chown alice file.txt

# Change group to dev
chgrp dev file.txt

# Change owner to alice and group to dev
chown alice:dev file.txt

# Change recursively for a directory
chown -R alice:dev /path/to/directory
```

### When to Use

Changing ownership is useful when files must be transferred to another user or shared with a team.

## Special Permissions

Linux also has special permission bits for advanced control:

### setuid (SUID)

- Represented as `s` in the owner's execute position (e.g., `-rwsr-xr-x`)
- Lets a program run with the permissions of the file owner
- Common on system utilities like `passwd` and `sudo`

### setgid (SGID)

- Represented as `s` in the group's execute position (e.g., `-rwxr-sr-x`)
- Lets a program run with the permissions of the group
- On directories, new files inherit the directory's group
- Useful for shared team directories

### Sticky Bit

- Represented as `t` in the others' execute position (e.g., `-rwxrwxrwt`)
- Usually used on shared directories
- Users can delete only their own files, not others' files
- Common on `/tmp` directory

### Setting Special Permissions

```bash
# Add SUID (numeric: 4000)
chmod u+s script.sh
chmod 4755 script.sh

# Add SGID (numeric: 2000)
chmod g+s directory/
chmod 2755 directory/

# Add Sticky Bit (numeric: 1000)
chmod o+t directory/
chmod 1777 directory/
```

## Default Permissions and Umask

When a new file or directory is created, Linux does not always give full permissions. The default permissions are controlled by **umask**, which removes certain permissions automatically.

### Why Umask Matters

This is important because it helps prevent new files from being too open by default. For example, a secure system may restrict write access for group or others right from the start.

### How Umask Works

```bash
# View current umask
umask

# Set a new umask (common: 0077 for secure permissions)
umask 0077

# Make it permanent by adding to ~/.bashrc
echo "umask 0077" >> ~/.bashrc
```

## Why Permissions Matter

File permissions protect confidentiality, integrity, and system stability by:

- Stopping ordinary users from changing sensitive files
- Preventing unauthorized execution of unsafe programs
- Protecting private data from being read by other users
- Maintaining system stability and security

### Critical in These Environments

- **Multi-user systems** — Multiple people sharing one server
- **Servers** — Production systems needing strict security
- **Shared lab computers** — University or office shared machines
- **Cloud environments** — Virtual instances with multiple tenants

### The Cost of Missing Permissions

Without proper permissions, one user could accidentally damage another user's files or expose private information, leading to data loss or security breaches.

---

## Real-World Example

Suppose a file has permission `-rw-r-----`:

```
-rw-r-----
│└┬┘└┬┘└┬┘
│ │  │  └─── Others: --- (no access)
│ │  └────── Group: r-- (read only)
│ └───────── Owner: rw- (read and write)
└──────────── File: - (regular file)
```

### Use Case

This is common for documents that should be:

- **Editable** by one person (the owner)
- **Readable** by a team (the group)
- **Hidden** from everyone else

---

## Useful Commands Reference

### Viewing Permissions

```bash
# View permissions in long format
ls -l

# View permissions with more details
ls -la

# View permissions for a specific file
ls -l filename.txt
```

### Changing Permissions

```bash
# Using numeric mode
chmod 755 script.sh
chmod 644 document.txt
chmod 700 secret.txt

# Using symbolic mode
chmod u+x script.sh
chmod go-w file.txt
chmod a-rwx secret.txt
chmod u=rw,g=r,o=r file.txt
```

### Changing Ownership

```bash
# Change owner
chown alice file.txt

# Change group
chgrp dev file.txt

# Change both
chown alice:dev file.txt

# Change recursively
chown -R alice:dev /path/to/directory
chgrp -R dev /path/to/directory
```

## Common Permission Patterns

| Permission | Numeric | Use Case |
|------------|---------|----------|
| `-rwxr-xr-x` | 755 | Scripts and executables |
| `-rw-r--r--` | 644 | Regular files |
| `-rw-------` | 600 | Private files |
| `-rwx------` | 700 | Private executable scripts |
| `drwxr-xr-x` | 755 | Directories (default) |
| `drwxrwx---` | 770 | Team directories |
| `drwxrwxrwt` | 1777 | Shared temp directories |

---

## Summary

Linux file permissions are essential for system security. By understanding:

- **Three permission groups** (owner, group, others)
- **Three basic permissions** (read, write, execute)
- **Numeric and symbolic modes** for changing permissions
- **Special permissions** for advanced control
- **Ownership and groups** for managing access

You can effectively protect your files and directories while maintaining proper access for authorized users.
