---
title: "Active Directory Group Policy"
published: 2025-04-20
description: "A simplified guide to Active Directory Group Policy."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

## Introduction to Group Policy

Group
 Policy is a powerful Windows feature that enables administrators to 
manage and configure settings for users and computers across a Windows 
environment. It serves as a critical tool for both system management and
 security enforcement in Active Directory domains.

### Key Characteristics:

- **Dual Application**: Can apply to both user and computer accounts
- **Hierarchical Structure**: Policies can be set at multiple levels (local, site, domain, OU)
- **Security Management**: Essential for implementing defense-in-depth security strategies
- **Attack Vector**: Can be abused by attackers for lateral movement and privilege escalation

## Group Policy Objects (GPOs)

### Definition and Structure

A Group Policy Object (GPO) is a virtual collection of policy settings with:

- Unique name and GUID identifier
- One or more policy settings (machine-level or AD context)
- Links to containers (OUs, domains, or sites)

### Application Scope

- Single GPO can link to multiple containers
- Multiple GPOs can apply to a single container
- Can target specific users, hosts, or groups via OU application

### Example Use Cases

1. **Security Policies**:
    - Custom password policies for different account types
    - Disabling removable media (USB devices)
    - Enforcing password-protected screensavers
    - Restricting access to cmd.exe and PowerShell
2. **System Management**:
    - Deploying software across the domain
    - Blocking unapproved software installations
    - Displaying logon banners
    - Disabling LM hash usage
3. **Session Management**:
    - Configuring Remote Desktop settings
    - Running logon/logoff and startup/shutdown scripts

## Default Password Policy Example

In Windows Server 2008 AD implementations, the default password complexity requires:

- Minimum 7 characters
- Characters from 3 of these 4 categories:
    - Uppercase (A-Z)
    - Lowercase (a-z)
    - Numbers (0-9)
    - Special characters (!@#$%^&* etc.)

## Group Policy Processing Order

Policies are processed hierarchically following the "Order of Precedence":

| Level | Description | Examples |
| --- | --- | --- |
| **Local Group Policy** | Host-specific settings outside the domain | Overwritten by domain policies |
| **Site Policy** | Settings for physical locations | Building-specific access controls, local printer mappings |
| **Domain-wide Policy** | Organization-wide settings | Password policies, desktop backgrounds, login banners |
| **Organizational Unit (OU)** | Role-specific settings | HR share drives, IT admin tools access |
| **Nested OU Policies** | Special permissions within sub-OUs | Security analyst-specific AppLocker settings |

### Key Processing Rules:

1. **Top-down processing**: Domain-level first, then child OUs
2. **Last writer wins**: OU-level GPOs override domain-level ones
3. **Computer policy precedence**: Computer settings override user settings
4. **Link Order**: When multiple GPOs link to the same OU, lower link order numbers have higher precedence

## Group Policy Management

### Management Tools

1. **Group Policy Management Console (GPMC)**
    - Primary graphical interface
    - Accessible via Administrative Tools on domain controllers
2. **PowerShell**
    - GroupPolicy module for command-line management
    - Enables scripting and automation

### Default GPOs

1. **Default Domain Policy**
    - Highest precedence
    - Applies to all users and computers
    - Best for domain-wide baseline settings
2. **Default Domain Controllers Policy**
    - Baseline security for all DCs
    - Customizable like any other GPO

## Advanced Policy Settings

### Enforcement and Inheritance

1. **Enforced (No Override)**
    - Prevents lower-level GPOs from overriding settings
    - Takes precedence over Block Inheritance
    - Example: Critical security settings that must apply domain-wide
2. **Block Inheritance**
    - Prevents higher-level GPOs from applying to an OU
    - Overridden by Enforced policies
    - Example: Test OU needing different settings than production

### Refresh Mechanism

- **Default refresh intervals**:
    - Standard clients: 90 minutes (±30 minute random offset)
    - Domain controllers: 5 minutes
- **Manual refresh**: `gpupdate /force` command
- **Customization**:
    - Can modify refresh interval via GPO
    - Located at: Computer Configuration → Policies → Administrative Templates → System → Group Policy

## Security Implications and Attack Vectors

### Defensive Uses

- Implementing security baselines
- Enforcing audit and logging policies
- Restricting dangerous functionalities
- Managing software installations

### Offensive Potential

Attackers can abuse GPOs for:

1. **Privilege Escalation**:
    - Adding admin rights to compromised accounts
    - Creating new privileged accounts
2. **Lateral Movement**:
    - Pushing malware to multiple systems
    - Establishing reverse shells
3. **Persistence**:
    - Maintaining access through scheduled tasks
    - Modifying group memberships

### Attack Path Example

As shown in BloodHound:

1. Attacker gains Domain Users membership
2. Discovers GPO modification rights through nested groups
3. Modifies GPO affecting high-value targets (admins, DCs)
4. Escalates privileges domain-wide

## Best Practices

1. **GPO Design**:
    - Use Default Domain Policy for universal settings
    - Create specialized GPOs for specific needs
    - Document GPO purposes and changes
2. **Security**:
    - Regularly audit GPO permissions
    - Monitor for unusual GPO modifications
    - Limit GPO modification rights
3. **Performance**:
    - Avoid overly frequent refresh intervals
    - Test GPOs in controlled environments before deployment
    - Keep number of GPOs per OU reasonable

## Conclusion

Group
 Policy is a cornerstone of Active Directory management, offering 
unparalleled control over user and computer configurations. Its proper 
implementation is crucial for both operational efficiency and security. 
However, its power also makes it a prime target for attackers, 
necessitating careful permission management and monitoring. 
Understanding these concepts is essential for both defending AD 
environments and identifying vulnerabilities during penetration tests.