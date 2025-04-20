---
title: NFS
published: 2025-04-20
description: "A simplified guide to NFS in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Footprinting
draft: false
---

## Network File System (NFS) in Penetration Testing

### Introduction to NFS
Network File System (NFS) is a distributed file system protocol developed by Sun Microsystems that enables remote file access across networks. Unlike SMB (used primarily in Windows environments), NFS is predominantly used in Unix/Linux systems.

### Key Characteristics:
- Allows remote file systems to appear as local
- Uses different protocol than SMB (incompatible with SMB clients)
- Internet standard for distributed file systems
- Authentication methods vary by version

### NFS Protocol Versions

| Version | Features |
|---------|----------|
| **NFSv2** | - Older standard widely supported<br>- Originally operated entirely over UDP |
| **NFSv3** | - Enhanced features (variable file sizes, better error reporting)<br>- Partial backward compatibility issues |
| **NFSv4** | - Kerberos authentication support<br>- Firewall friendly (single port 2049)<br>- ACL support<br>- Stateful protocol<br>- Performance improvements |
| **NFSv4.1** | - Cluster server support (pNFS extension)<br>- Multipathing capability<br>- Session trunking |

### NFS Security Model
NFS relies on the ONC-RPC/SUN-RPC protocol (ports 111 TCP/UDP) with External Data Representation (XDR) for system-independent data exchange.

**Authentication Considerations:**
- No built-in authentication/authorization mechanisms
- Relies on RPC protocol options for authentication
- Most common method uses UNIX UID/GID and group memberships
- Server translates client user info to file system format
- Potential UID/GID mapping mismatches between client/server

### NFS Configuration
Configured via `/etc/exports` file which defines accessible filesystems and permissions.

### Sample /etc/exports:
```bash
# NFSv2/v3 example:
/srv/homes hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)

# NFSv4 example:
/srv/nfs4 gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
```

### Common Export Options:

| Option | Description |
| --- | --- |
| **rw** | Read/write permissions |
| **ro** | Read-only permissions |
| **sync** | Synchronous data transfer (more reliable) |
| **async** | Asynchronous data transfer (faster) |
| **secure** | Restricts to ports below 1024 |
| **no_subtree_check** | Disables subdirectory tree checking |
| **root_squash** | Maps root UID/GID to anonymous (security feature) |

## Dangerous NFS Configurations

These settings can create significant security vulnerabilities:

| Option | Risk |
| --- | --- |
| **rw** | Unrestricted write access |
| **insecure** | Allows use of ports above 1024 |
| **nohide** | Exposes mounted filesystems |
| **no_root_squash** | Preserves root UID/GID (critical vulnerability) |

## NFS Footprinting Techniques

### Nmap Scanning

```bash
sudo nmap -p111,2049 -sV -sC <target_IP>`
```

**Key Ports:**

- 111/tcp - RPC portmapper
- 2049/tcp - NFS service

### NFS-specific Nmap Scripts

bash

```bash
sudo nmap --script nfs* <target_IP> -sV -p111,2049
```

**Script Output Includes:**

- Share contents
- Mount points
- File permissions
- UID/GID mappings

## Mounting NFS Shares

### Discovering Available Shares

```bash 
showmount -e <target_IP>`
```

### Mounting a Share Locally

```bash
mkdir target-NFS
sudo mount -t nfs <target_IP>:/path ./target-NFS/ -o nolock
```

### Examining Mounted Shares

```bash
`ls -l /mnt/nfs/      # View with usernames
ls -n /mnt/nfs/      # View with UID/GID numbers`
```

## Exploitation Techniques

### 1. No Root Squash Vulnerability

When **no_root_squash** is enabled:

1. Create malicious files as local root
2. Files will maintain root permissions on server
3. Potentially overwrite critical system files

### 2. UID/GID Manipulation

1. Identify interesting files and their owners
2. Create matching local users with same UID/GID
3. Access restricted files through NFS mount

### 3. SUID Binary Attacks

1. Upload crafted SUID binary to writable share
2. Execute via other access vectors (SSH, etc.)
3. Escalate privileges using binary's permissions

## Unmounting Shares

```bash
`cd ~  # Ensure you're not in mounted directory
sudo umount ./target-NFS
```

### Security Best Practices

### For Administrators:

- Always use **root_squash** (default in most modern implementations)
- Restrict exports to specific IPs/networks when possible
- Implement Kerberos authentication (NFSv4+)
- Regularly audit **/etc/exports** configurations
- Monitor NFS access logs

### For Penetration Testers:

- Always verify permissions before modifying files
- Document findings thoroughly
- Consider impact before exploiting write access
- Check for sensitive files (SSH keys, configs, etc.)

```bash 
This markdown document provides:
1. Comprehensive coverage of NFS in security testing
2. Clear section organization with headers
3. Properly formatted tables and code blocks
4. Practical commands and techniques
5. Both offensive and defensive perspectives
6. Version-specific details
7. Real-world exploitation scenarios
```

The content flows logically from protocol fundamentals through discovery to exploitation and finally security recommendations.