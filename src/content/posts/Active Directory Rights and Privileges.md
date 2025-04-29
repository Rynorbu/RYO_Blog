---
title: "Active Directory Rights and Privileges"
published: 2025-04-20
description: "A simplified guide to Active Directory Rights and Privileges."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

# Active Directory Rights and Privileges

## Understanding Rights vs. Privileges

### Rights

- **Definition**: Permissions to access objects (files, folders, printers, etc.)
- **Characteristics**:
    - Typically assigned to users or groups
    - Deal with resource access control
    - Example: Read/Write/Execute permissions on a file share

### Privileges

- **Definition**: Authority to perform specific system-level actions
- **Characteristics**:
    - Granted to security principals (users/groups)
    - Enable system operations beyond simple access
    - Example: Shut down a system, debug programs, load drivers

**Key Difference**: Rights control *what* you can access, privileges control *what actions* you can perform.

## Built-in AD Security Groups

These default groups confer significant privileges and are prime targets for attackers:

### High-Privilege Groups

| Group Name | Scope | Key Privileges | Security Considerations |
| --- | --- | --- | --- |
| Administrators | Domain Local | Full system control | Membership should be extremely limited |
| Domain Admins | Global | Full domain control | Members are local admins on all domain-joined systems |
| Enterprise Admins | Universal | Forest-wide control | Only exists in root domain of forest |
| Schema Admins | Universal | Modify AD schema | Only exists in root domain of forest |
| Backup Operators | Domain Local | Backup/restore files | Can access sensitive system files (SAM, NTDS.dit) |
| Account Operators | Domain Local | Manage user accounts | Cannot manage admin accounts but can create new ones |
| Server Operators | Domain Local | Administer domain controllers | Can modify services and access shares on DCs |
| Print Operators | Domain Local | Manage printers on DCs | Potential driver loading privilege escalation |
| DnsAdmins | Domain Local | Manage DNS infrastructure | Can load malicious DLLs leading to SYSTEM access |

### Other Important Groups

| Group Name | Scope | Purpose | Security Notes |
| --- | --- | --- | --- |
| Remote Desktop Users | Domain Local | RDP access | Can be used for lateral movement |
| Hyper-V Administrators | Domain Local | Virtualization management | Equivalent to Domain Admins if virtual DCs exist |
| Protected Users | Global | Enhanced security | Prevents certain credential theft techniques |
| Event Log Readers | Domain Local | Read event logs | Useful for monitoring but can reveal sensitive info |

## User Rights Assignment

These privileges can be assigned via Group Policy or local security policy:

### Critical Privileges to Monitor

| Privilege | Technical Name | Potential Abuse |
| --- | --- | --- |
| Backup files/directories | SeBackupPrivilege | Extract password hashes from SAM/NTDS.dit |
| Debug programs | SeDebugPrivilege | Dump LSASS memory for credentials |
| Impersonate client | SeImpersonatePrivilege | Privilege escalation via token impersonation |
| Load/unload drivers | SeLoadDriverPrivilege | Install malicious drivers |
| Take ownership | SeTakeOwnershipPrivilege | Gain access to protected files/objects |
| Remote interactive logon | SeRemoteInteractiveLogonRight | RDP access to systems |
| Act as part of OS | SeTcbPrivilege | Complete system control |

### Viewing Assigned Privileges

To check privileges for current user:

powershell

`whoami /priv`

**Note**: User Account Control (UAC) filters privileges in non-elevated sessions. Always check both elevated and non-elevated contexts.

## Security Best Practices

### Group Management

1. **Principle of Least Privilege**:
    - Only assign necessary privileges
    - Regularly audit group memberships
    - Remove users from groups when no longer needed
2. **Nesting Strategy**:
    - Follow AGDLP (Accounts → Global Groups → Domain Local Groups → Permissions)
    - Limit nesting depth to 3 levels maximum
    - Avoid circular nesting
3. **High-Privilege Groups**:
    - Keep membership minimal
    - Use dedicated admin accounts (not daily-use accounts)
    - Monitor changes with alerts

### Privilege Management

1. **User Rights Assignment**:
    - Restrict dangerous privileges (SeDebug, SeImpersonate, etc.)
    - Document all privilege assignments
    - Review through Group Policy Analysis
2. **Service Accounts**:
    - Never add to high-privilege groups
    - Use Managed Service Accounts (gMSA) when possible
    - Implement strict password policies

### Monitoring and Auditing

1. **Enable Detailed Logging**:
    - Audit privilege use
    - Track group membership changes
    - Monitor sensitive group access
2. **Regular Reviews**:
    - Quarterly privilege audits
    - Immediate review when adding to sensitive groups
    - Automated reports on privileged accounts

## Attack Surface Reduction

1. **Protect Domain Controllers**:
    - Limit local logon rights
    - Restrict RDP/WinRM access
    - Monitor DC privilege use
2. **Secure Administrative Access**:
    - Implement Privileged Access Workstations (PAWs)
    - Use jump servers for admin access
    - Enforce multi-factor authentication
3. **Legacy Protection**:
    - Review Pre-Windows 2000 Compatible Access
    - Disable unnecessary legacy protocols
    - Migrate from deprecated features

## Common Misconfigurations

1. **Overprivileged Service Accounts**:
    - Often added to Domain Admins unnecessarily
    - Lack of proper service principal names (SPNs)
2. **Excessive Nested Groups**:
    - Leads to unintended privilege inheritance
    - Difficult to track effective permissions
3. **Privilege Creep**:
    - Users accumulate unnecessary rights over time
    - Lack of periodic rights reviews
4. **UAC Misunderstanding**:
    - Assuming privileges are always filtered
    - Not recognizing bypass techniques