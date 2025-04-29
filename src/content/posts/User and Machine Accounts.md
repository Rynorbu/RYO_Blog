---
title: "User and Machine Accounts"
published: 2025-04-20
description: "A simplified guide to User and Machine Accounts."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

# User and Machine Accounts

## User Accounts Overview

User 
accounts are fundamental security principals that enable authentication 
and authorization in both local systems and Active Directory (AD) 
environments. They serve as digital identities for both people and 
programs (like system services) to access resources based on assigned 
rights.

### Authentication Process

- When a user logs in, the system verifies their credentials (typically username/password)
- Upon successful authentication, the system creates an **access token**
- This token contains:
    - The user's security identity (SID)
    - Group memberships
    - Privileges assigned to the user

### Purpose of User Accounts

1. **Employee/Contractor Access**: Allow personnel to log into computers and access network resources
2. **Service Context**: Run programs or services under specific security contexts (e.g.,
running as a privileged user instead of a network service account)
3. **Resource Management**: Control access to objects like network shares, files, and applications

### Group Management

- Users can be assigned to groups that contain one or more members
- Groups simplify administration by:
    - Allowing privilege assignment at the group level (inherited by all members)
    - Making it easier to grant/revoke rights across multiple users simultaneously

## Active Directory User Accounts

AD
 user accounts are central to domain management and present a 
significant attack surface due to their potential privileges and common 
misconfigurations.

### Account Types in AD

1. **Standard User Accounts**: Basic accounts for regular employees
2. **Admin Accounts**: Elevated privilege accounts (may be separate from standard accounts for IT staff)
3. **Service Accounts**: Used by applications/services running in the background

### Common AD Account Practices

- Companies typically maintain at least one AD account per user
- IT staff may have multiple accounts (standard and admin)
- Organizations often retain disabled accounts for former employees in OUs like "FORMER EMPLOYEES"
- Service accounts can significantly increase the total number of active accounts (e.g., 1,200 accounts for 1,000 employees)

### Privilege Spectrum

User accounts can range from:

- **Read-only users** (basic Domain User permissions)
- **Enterprise Admins** (complete control over all domain objects)
- Many custom permission levels in between

### Security Challenges

- Users are often the "weakest link" due to:
    - Weak or shared passwords
    - Unauthorized software installation
    - Admin misconfigurations
- Defense-in-depth strategies are needed to mitigate these risks

## Local Accounts

Local accounts exist only on a specific computer and don't provide domain-wide access.

### Default Local Accounts

1. **Administrator**
    - SID: S-1-5-domain-500
    - First account created during Windows installation
    - Full control over the local system
    - Cannot be deleted or locked, but can be disabled/renamed
    - Disabled by default in Windows 10/Server 2016+
2. **Guest**
    - Disabled by default
    - Designed for temporary access with limited rights
    - Security risk if enabled (blank password by default)
3. **SYSTEM (NT AUTHORITY\SYSTEM)**
    - Highest privilege level on Windows
    - Used by the OS for internal functions
    - No user profile, not visible in User Manager
    - Full control over all system files by default
4. **Network Service**
    - Predefined local account for service control
    - Presents computer credentials to remote services
5. **Local Service**
    - Minimal privilege account for service control
    - Presents anonymous credentials to the network

## Domain Users vs. Local Users

### Domain Users

- Authenticated by domain controllers
- Can access resources across the domain
- Permissions are managed centrally in AD
- Can log into any domain-joined computer

### Local Users

- Authenticated only by the local machine
- Permissions apply only to the local system
- Cannot access domain resources
- Must be created/managed on each individual computer

### Special Domain Account: KRBTGT

- Service account for Key Distribution Center (KDC)
- Critical for Kerberos authentication
- Common attack target (e.g., Golden Ticket attacks)
- Compromise can lead to domain-wide access

## User Naming Attributes

Active Directory uses several attributes to identify and manage user objects:

1. **UserPrincipalName (UPN)**
    - Primary logon name (often matches email format)
    - Example: [username@domain.com](https://mailto:username@domain.com/)
2. **ObjectGUID**
    - Unique, immutable identifier
    - Remains constant even if user is moved/deleted
3. **SAMAccountName**
    - Legacy logon name format
    - Used for backward compatibility
4. **objectSID**
    - Security Identifier (SID)
    - Used for security interactions and group membership
5. **sIDHistory**
    - Contains previous SIDs after domain migrations
    - Can lead to privilege escalation if not properly cleaned

## Domain-Joined vs. Non-Domain-Joined Machines

### Domain-Joined Computers

- Centrally managed through Group Policy
- Users can log in from any domain-joined computer
- Easy resource sharing within the domain
- Automatic configuration updates from domain controllers
- Typical in enterprise environments

### Non-Domain-Joined (Workgroup) Computers

- No central management
- Users only exist on local machine
- More difficult resource sharing
- Users manage their own systems
- Common in home/small business environments

## Machine Accounts and SYSTEM Access

- Machine accounts have similar rights to domain user accounts
- SYSTEM-level access on a domain-joined host provides:
    - Read access to much domain data
    - Potential launching point for AD attacks
- Can be obtained through:
    - Remote code execution exploits
    - Local privilege escalation