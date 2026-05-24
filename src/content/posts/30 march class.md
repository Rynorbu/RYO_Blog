---
title: "Docker Exec, Attach, and Shells"
published: 2026-03-30
description: "Understanding docker exec, shell differences, and container interaction modes."
# image: "../../assets/images/my_photo.jpg"
tags: ["Docker", "Docker Exec", "Bash", "Zsh", "DevOps", "Containers", "DSO101"]
category: DevOps
draft: false
---

## Introduction

This guide covers three important Docker concepts: running commands inside containers with `docker exec`, understanding different shells like Bash and Zsh, and managing container interaction modes with attach and detach.

---

## Docker Exec — Running Commands Inside Containers

### What is Docker Exec?

`docker exec` is a command used to run a new command inside a container that is already running. It is very useful when you want to check files, test commands, inspect logs, or troubleshoot a container without stopping it.

**Important:** `docker exec` does not create a new container; it only runs a command inside the existing one.

### Basic Syntax

```bash
docker exec [OPTIONS] <container-id> <command>
```

### Simple Example

```bash
# Run a single command inside a container
docker exec <container-id> ls /etc

# Shows the files in the /etc directory inside the container
```

### Output

```
hostname
hosts
hostname.conf
host.conf
...
```

---

## Interactive Terminal Access

### Opening a Shell Inside a Container

To open a shell inside the container for interactive exploration, use the `-it` flags:

```bash
# Open Bash shell (if available)
docker exec -it <container-id> bash

# Or use sh (shell) - more universally available
docker exec -it <container-id> sh
```

### Understanding the -it Flags

| Flag | Purpose |
|------|---------|
| `-i` | Keep STDIN open even if not attached (allows input) |
| `-t` | Allocate a pseudo-TTY (terminal-like interface) |
| `-it` | Combined: interactive terminal access |

**Without `-it`:** The command runs but you cannot interact with it.  
**With `-it`:** You can type commands and see output in real-time.

### Examples

```bash
# Interactive - you can type commands
docker exec -it mycontainer bash
root@abc123:/# ls
root@abc123:/# pwd
root@abc123:/# exit

# Non-interactive - just get the output
docker exec mycontainer ls /home
user1
user2
user3
```

---

## When to Use Docker Exec

### Use Cases

`docker exec` is commonly used for:

#### Debugging

- Check if a config file exists
- Verify if a package is installed
- Confirm a folder contains the correct data

#### Maintenance Tasks

- Read log files
- Check environment variables
- Confirm the operating system version
- Inspect application state

#### Quick Checks

- View file contents
- Check directory structure
- Test commands without rebuilding

### Why Exec is Better Than Rebuilding

```bash
# Instead of rebuilding and restarting:
# docker build -t myapp .
# docker run myapp

# Just use exec:
docker exec <container-id> cat /var/log/app.log
```

**Benefits:**

- Fast — No rebuild or restart needed
- Non-destructive — Container keeps running
- Convenient — Immediate feedback

---

## Non-Interactive Commands

### Running Single Commands

When you only want the output of one command, you don't need `-it`:

```bash
# Non-interactive command - good for simple output
docker exec <container-id> cat /etc/os-release

# Output example:
NAME="Ubuntu"
VERSION="20.04.3 LTS"
ID=ubuntu
ID_LIKE=debian
```

### Examples

```bash
# Check Python version
docker exec mycontainer python3 --version

# List environment variables
docker exec mycontainer env

# Check disk usage
docker exec mycontainer df -h

# View a log file
docker exec myapp tail -f /var/log/app.log
```

### When to Use Without -it

Use non-interactive mode when:

- You need output from a single command
- You don't need to type additional commands
- The command is simple and completes quickly
- You want to automate in scripts

---

## The Zsh Wildcard Problem

### What is Zsh?

Zsh is a Unix shell like Bash, but it has extra features such as:

- Better tab completion
- Spelling correction
- Advanced themes
- More aggressive pattern expansion

### The Problem: Wildcard Expansion

A common problem happens when you use a wildcard like `*` in a command.

#### Example That Fails

```bash
docker exec 0d56f7baf07d cat /etc/*release
```

**What happens:**

1. Zsh sees the `*` in `/etc/*release`
2. Zsh tries to expand `*` on your **host machine**
3. If no file matches on the host, Zsh gives an error
4. Docker never even receives the command

### Error Message

```
zsh: no matches found: /etc/*release
```

**This is NOT a Docker problem.** The host shell is interpreting the `*` too early.

---

## Fixing the Wildcard Problem

### Solution 1: Use Quotes

Put the path in quotes so the host shell doesn't expand it:

```bash
# Safe - quotes protect the wildcard
docker exec 0d56f7baf07d cat '/etc/*release'

# Or double quotes
docker exec 0d56f7baf07d cat "/etc/*release"
```

### Solution 2: Use sh -c

Tell the container shell to handle the wildcard:

```bash
# Safest approach - container shell does the expansion
docker exec 0d56f7baf07d sh -c 'cat /etc/*release*'

# The single quotes prevent host shell expansion
# The container's sh does the wildcard expansion
```

### Solution 3: Exact Filename

If you know the exact filename, just use it:

```bash
# No wildcard needed if you know the filename
docker exec 0d56f7baf07d cat /etc/os-release
```

### Comparison

| Approach | Safety | Clarity |
|----------|--------|---------|
| `cat '/etc/*release'` | Good | Clear |
| `sh -c 'cat /etc/*release*'` | Best | Clear but complex |
| `cat /etc/os-release` | Best | Clearest |

---

## Bash — Bourne Again Shell

### What is Bash?

**Bash** stands for **Bourne Again Shell**. It is one of the most widely used command-line interpreters and scripting languages in Linux and Unix-like systems.

### Common Uses

Bash lets users:

- Type commands directly
- Automate tasks with scripts
- Write shell scripts
- Manage files and system tasks

### Bash in Docker

In many Docker containers, Bash is included:

```bash
# Enter Bash shell inside container
docker exec -it <container-id> bash

# If Bash is not available, use sh instead
docker exec -it <container-id> sh
```

### Why Bash is Popular

- **Simple** — Easy to learn for beginners
- **Powerful** — Can automate complex tasks
- **Standard** — Available on most Linux systems
- **Widely used** — Supported by most applications

---

## Bash vs Zsh

### Key Differences

| Feature | Bash | Zsh |
|---------|------|-----|
| **Popularity** | Very common, standard | Growing, feature-rich |
| **Learning curve** | Simple | Steeper, more features |
| **Tab completion** | Basic | Advanced/intelligent |
| **Spelling correction** | No | Yes |
| **Pattern expansion** | Conservative | Aggressive |
| **Wildcard handling** | Predictable | Can expand early |
| **Scripting** | Very reliable | Reliable, more features |

### Bash Characteristics

```bash
# Simple, straightforward
$ echo "Hello from Bash"
Hello from Bash

# Predictable wildcard behavior
$ ls /etc/*release    # Only expands if files exist
```

### Zsh Characteristics

```bash
# More features and completion
$ echo "Hello from Zsh"
Hello from Zsh

# More aggressive pattern expansion
$ ls /etc/*release    # May expand even when you don't expect it
```

### For Docker Commands

**This matters because:**

- The shell on your computer processes the command first
- If your shell is Zsh, you must be more careful with special characters like `*`
- Bash is more predictable for Docker commands

### Best Practice

```bash
# If using Zsh with Docker, quote your wildcards
docker exec container sh -c 'cat /etc/*release'

# Bash users usually don't have this issue
docker exec container cat /etc/*release
```

---

## Docker Attach — Connect to Container Process

### What is Docker Attach?

`docker attach` connects your terminal directly to the main process of a running container. This means you can see the output of that process in your terminal, almost like the container is running in the foreground.

### Basic Syntax

```bash
docker attach <container-id>
```

### When to Use Attach

Attach is useful when you want to:

- Watch what the container is doing in real time
- Monitor a server or service output
- See live logs from the running process
- Follow container execution

### Example

```bash
# Start a server container in the background
docker run -d --name web nginx

# Later, attach to see its output
docker attach web

# You'll see the nginx access logs in real-time
```

### Important: Attach vs Exec

| Command | Purpose | Connects To |
|---------|---------|------------|
| `docker attach` | Connect to main process | Main container process |
| `docker exec` | Run a new command | New separate command |

**Key difference:**

- `attach` = Connect to what's already running
- `exec` = Start something new

### Detaching Without Stopping

To disconnect from attach mode without stopping the container:

```
Press: Ctrl + P, then Ctrl + Q
```

This leaves the container running in the background.

---

## Detach Mode — Background Execution

### What is Detach Mode?

Detach mode means the container runs in the background, and your terminal is free for other work. This is very useful when running services that need to keep running.

### Running in Detach Mode

```bash
# -d flag means detached mode
docker run -d nginx

# Container starts in the background
# Terminal returns immediately
```

### Foreground vs Background

| Mode | Terminal | Container | Usage |
|------|----------|-----------|-------|
| **Foreground (Attach)** | Blocked | Visible | Testing, debugging |
| **Background (Detach)** | Free | Hidden | Production services |

### Examples

```bash
# Run a web server in background
docker run -d --name web nginx

# Terminal is free immediately
# You can run more commands
docker ps

# To see the output
docker logs web
```

### Practical Workflow

```bash
# Start container in background
docker run -d --name myapp myapp:latest

# Continue with other work
docker run -d --name db postgres

# Check status
docker ps

# View logs when needed
docker logs myapp

# Stop when done
docker stop myapp
docker stop db
```

---

## Frontend and Background Concept

### Simple Way to Remember

- **Attach mode** = Foreground = Terminal is connected to container
- **Detach mode** = Background = Container runs independently

### What Happens

#### Foreground (Attach Mode)

```bash
docker run -it ubuntu bash

# Your terminal is now INSIDE the container
# You must exit to get back to your host terminal
```

#### Background (Detach Mode)

```bash
docker run -d nginx

# Container starts
# You immediately get your terminal back
# Container keeps running in background
```

### In Attach Mode

```
Your Terminal ←→ Container Process
│                      │
Still connected        Still running
Can't type new cmds    Visible output
```

### In Detach Mode

```
Your Terminal      Container Process
│                  │
Free to use        Running independently
Can type new cmds  Output hidden
Can check with logs
```

---

## Important Differences — Quick Reference

### Core Concepts

| Concept | Purpose | Behavior |
|---------|---------|----------|
| **docker exec** | Run command in existing container | Separate command, doesn't affect main process |
| **docker attach** | Connect to main container process | Direct connection, shows container output |
| **Detached mode (-d)** | Run container in background | Terminal is free immediately |
| **Interactive mode (-it)** | Interactive shell/terminal | Can type commands manually |

### Which One to Use?

```bash
# Start a web server that needs to keep running
docker run -d nginx

# Later, check what's happening
docker logs nginx

# Or open a shell to inspect
docker exec -it nginx bash

# To watch output in real-time
docker attach nginx
```

---

## Practical Examples

### Scenario 1: Check OS Release

You have a container running Ubuntu, and you want to check its release information.

#### Option 1: Simple output

```bash
docker exec <container-id> cat /etc/os-release
```

#### Option 2: With wildcard (if needed)

```bash
# If using Zsh, be careful with *
docker exec <container-id> sh -c 'cat /etc/*release*'
```

#### Option 3: Interactive exploration

```bash
# Open shell for manual exploration
docker exec -it <container-id> bash
```

### Scenario 2: Running a Web Server

```bash
# 1. Start server in background
docker run -d --name web -p 8080:80 nginx

# 2. Check if it's running
docker ps

# 3. View startup logs
docker logs web

# 4. Connect to see live output
docker attach web

# 5. Exit without stopping (Ctrl + P, Ctrl + Q)

# 6. Or open a shell to inspect
docker exec -it web bash
```

### Scenario 3: Database Container

```bash
# 1. Run database in background
docker run -d \
  --name db \
  -e POSTGRES_PASSWORD=secret \
  postgres

# 2. Check if it started
docker logs db

# 3. Connect to database shell
docker exec -it db psql -U postgres

# 4. Or run a quick command
docker exec db psql -U postgres -c "SELECT version();"
```

---

## Common Use Patterns

### Debugging a Container

```bash
# 1. Check if container is running
docker ps -a

# 2. View logs
docker logs container_name

# 3. Follow logs in real-time
docker logs -f container_name

# 4. Open a shell to investigate
docker exec -it container_name bash

# 5. Check resources
docker stats container_name

# 6. Inspect detailed info
docker inspect container_name
```

### Maintenance Tasks

```bash
# Check logs
docker exec container_name tail -f /var/log/app.log

# View configuration
docker exec container_name cat /etc/app/config.yml

# Check environment variables
docker exec container_name env

# Verify installed packages
docker exec container_name dpkg -l
```

### Quick Tests

```bash
# Test connectivity
docker exec container_name ping google.com

# Check Python version
docker exec container_name python3 --version

# Run a script
docker exec container_name python3 /app/check.py

# Check disk space
docker exec container_name df -h
```

---

## Short Revision Notes

### Commands

| Command | Purpose |
|---------|---------|
| `docker exec <id> <cmd>` | Run command in running container |
| `docker exec -it <id> bash` | Open interactive shell |
| `docker attach <id>` | Connect to main container process |
| `docker run -d` | Start container in background |
| `docker logs <id>` | View container output |

### Flags

| Flag | Meaning |
|------|---------|
| `-i` | Keep STDIN open (allows input) |
| `-t` | Allocate pseudo-TTY (terminal) |
| `-it` | Interactive terminal |
| `-d` | Detached mode (background) |

### Shells

| Shell | Characteristics |
|-------|-----------------|
| **Bash** | Simple, standard, predictable |
| **Zsh** | Advanced, more features, expands `*` aggressively |

### Wildcard Issues

- **Zsh problem:** Expands `*` on host before Docker receives it
- **Solution:** Use quotes or `sh -c`
- **Example:** `docker exec id sh -c 'cat /etc/*release'`

---

## Easy Way to Remember

Think of it like this:

| Concept | Analogy |
|---------|---------|
| **docker exec** | Go inside and run one task |
| **docker attach** | Connect to the main running process |
| **docker detach (-d)** | Let it keep running in the background |
| **Interactive (-it)** | You can type commands (like being there in person) |
| **Non-interactive** | Just run one command and get output |
| **Bash** | Standard, simple shell |
| **Zsh** | Advanced shell that might interfere with `*` |

---

## Quick Decision Tree

### "How do I interact with a running container?"

```
Need to run a command?
├─ Want to type more commands? → docker exec -it <id> bash
├─ Just need one command output? → docker exec <id> <command>
└─ Want to see live output? → docker attach <id>

Want to start a new container?
├─ Need to interact with it? → docker run -it <image>
└─ Want it to keep running? → docker run -d <image>

Wildcard not working in Zsh?
└─ Use: docker exec <id> sh -c 'cat /etc/*release'
```

---

## Summary

### Key Takeaways

1. **docker exec** runs a command inside a running container without stopping it
2. **-it flags** make exec interactive so you can type commands
3. **Zsh wildcards** like `*` can be tricky; use quotes or `sh -c`
4. **Bash** is simple and standard; **Zsh** is advanced but can expand wildcards aggressively
5. **docker attach** connects to the main container process
6. **Detached mode (-d)** runs containers in the background
7. **Foreground** = attached to terminal; **Background** = running independently

### When to Use Each

- Use **exec** for debugging and quick commands
- Use **attach** to watch real-time output
- Use **-d** for services that need to keep running
- Use **-it** when you need an interactive shell

### Best Practices

- Always use quotes with wildcards: `'command'`
- For complex commands, use `sh -c 'command'`
- Use `docker logs` instead of `attach` for permanent records
- Remember that `exec` is non-destructive — container keeps running
