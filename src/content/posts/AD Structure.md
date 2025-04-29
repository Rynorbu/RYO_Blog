---
title: "Active Directory Structure"
published: 2025-04-20
description: "A simplified guide to Active Directory Structure."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

## Introduction to Active Directory

Active
 Directory (AD) is Microsoft's directory service for Windows network 
environments that provides centralized management of an organization's 
resources. As a distributed, hierarchical database, AD serves as the 
foundation for authentication and authorization in Windows domain 
environments.

### Key Characteristics:

- **Directory Services**: Stores and manages information about network resources (AD DS - Active Directory Domain Services)
- **Centralized Management**: Controls users, computers, groups, policies, and more from a single point
- **Authentication/Authorization**: Manages user credentials and access rights
- **Evolution**: Introduced with Windows Server 2000, continually updated but maintains backward compatibility
- **Security Challenges**: Not secure by default and complex to configure properly, especially in large environments

## Active Directory Components

### Core Structural Elements

1. **Forest**:
    - The top-level security boundary containing all AD objects
    - Can contain multiple domains
    - Represents the complete AD instance for an organization
2. **Domain**:
    - Container for objects (users, computers, groups)
    - Security boundary for policies and authentication
    - Can contain child domains (subdomains)
3. **Organizational Units (OUs)**:
    - Administrative containers within domains
    - Enable logical grouping of objects
    - Allow granular application of Group Policies
    - Can be nested (OUs within OUs)

### Default and Custom OUs

Typical default OUs include:

- Domain Controllers
- Users
- Computers

Administrators can create custom OUs to reflect organizational structure:

- Departments (HR, Finance, IT)
- Geographic locations
- Functional roles
- Security classifications

## Active Directory Hierarchy Example

A sample AD structure for `INLANEFREIGHT.LOCAL`:

```jsx
INLANEFREIGHT.LOCAL/
├── ADMIN.INLANEFREIGHT.LOCAL
│   ├── GPOs
│   └── OU
│       └── EMPLOYEES
│           ├── COMPUTERS
│           │   └── FILE01
│           ├── GROUPS
│           │   └── HQ Staff
│           └── USERS
```

### Breakdown:

- **Root Domain**: `INLANEFREIGHT.LOCAL`
- **Subdomains**:
    - `ADMIN.INLANEFREIGHT.LOCAL`
    - `CORP.INLANEFREIGHT.LOCAL`
    - `DEV.INLANEFREIGHT.LOCAL`
- **OU Structure**: Shows nested OUs containing computers, groups, and users

## Trust Relationships

### Understanding Domain Trusts

Trust relationships enable authentication across domain boundaries:

1. **Within a Forest**:
    - Automatic two-way transitive trusts between parent and child domains
    - Allows seamless access throughout the forest
2. **Between Forests**:
    - Must be explicitly configured
    - Can be one-way or two-way
    - Can be transitive or non-transitive

### Trust Example

Graphical representation of two forests with trust:

`INLANEFREIGHT.LOCAL (Forest A)      FREIGHTLOGISTICS.LOCAL (Forest B)
       ↑       ↑       ↑                     ↑       ↑       ↑
      CORP    DEV    ADMIN                 CORP    DEV    ADMIN
       ⇵                                   ⇵
(Bidirectional Forest Trust)`

Key points about this trust configuration:

- The root domains have a bidirectional trust
- Child domains automatically trust their parent (vertical arrows)
- Child domains in Forest A don't automatically trust child domains in Forest B
- Additional trusts would be needed for direct child-to-child domain access

## Active Directory Objects

### Enumerable Objects

Even standard user accounts can enumerate most AD objects:

- Domain computers and users
- Group memberships and structures
- Organizational Unit hierarchy
- Password policies and GPOs
- Domain trusts and functional levels
- Access Control Lists (ACLs)

### Security Implications

This visibility creates several security considerations:

1. **Information Exposure**:
    - Attackers can map the entire AD structure
    - Helps identify high-value targets and misconfigurations
2. **Attack Path Identification**:
    - Reveals trust relationships that could be exploited
    - Shows group memberships for privilege escalation
3. **Lateral Movement**:
    - Identifies all systems in the domain
    - Reveals service accounts and their permissions

## Active Directory Security Considerations

### Common Vulnerabilities

1. **Default Configurations**:
    - Overly permissive access rights
    - Legacy authentication protocols enabled
    - Weak password policies
2. **Trust Issues**:
    - Overly permissive cross-forest trusts
    - SID filtering not properly configured
    - Inadequate monitoring of trust relationships
3. **Structural Problems**:
    - Overly complex OU structures
    - Inconsistent GPO application
    - Poorly managed service accounts

### Best Practices

1. **Secure Design**:
    - Implement principle of least privilege
    - Regularly review and clean up AD objects
    - Monitor for unusual enumeration attempts
2. **Trust Management**:
    - Minimize cross-forest trusts when possible
    - Implement SID filtering where appropriate
    - Document all trust relationships
3. **Monitoring**:
    - Audit sensitive AD changes
    - Monitor for unusual authentication patterns
    - Track Group Policy modifications

## Practical Implications for Security Professionals

### For Defenders

1. **Hardening Active Directory**:
    - Implement Microsoft's Active Directory security baseline
    - Regularly audit permissions and group memberships
    - Monitor for suspicious enumeration activities
2. **Incident Response**:
    - Understand common AD attack vectors
    - Have procedures for securing compromised accounts
    - Maintain backups of critical AD components

### For Penetration Testers

1. **Enumeration Techniques**:
    - Leverage built-in tools like PowerShell AD module
    - Use specialized tools like BloodHound for mapping
    - Focus on trust relationships and ACLs
2. **Exploitation Paths**:
    - Identify misconfigured trusts
    - Look for overly permissive GPOs
    - Hunt for privileged service accounts

## Conclusion

Active Directory's hierarchical structure provides powerful management  capabilities but also introduces significant security challenges if not  properly configured. Understanding the forest-domain-OU hierarchy, trust relationships, and object visibility is crucial for both securing AD  environments and identifying potential attack paths. The distributed  nature of AD and its backward compatibility requirements make it  particularly vulnerable to misconfigurations that can be exploited by  attackers. Proper design, ongoing maintenance, and vigilant monitoring  are essential for maintaining a secure Active Directory environment.