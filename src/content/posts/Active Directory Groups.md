---
title: "Active Directory Groups"
published: 2025-04-20
description: "A simplified guide to Active Directory Groups."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

# Active Directory Groups

## Introduction to AD Groups

Active
 Directory groups are fundamental security principals that enable 
efficient management of permissions and access control. They serve as 
containers for users, computers, and other objects to simplify 
administration and security management.

### Key Benefits of Using Groups:

- **Simplified administration**: Assign permissions once to a group rather than individually to users
- **Efficient auditing**: Easier to track who has access to resources
- **Flexible management**: Quickly modify access by adding/removing group members
- **Reduced overhead**: Minimizes administrative workload for permission management

## Group Types

Active Directory supports two primary group types, each serving distinct purposes:

### 1. Security Groups

- **Primary Purpose**: Assign permissions and rights to resources
- **Key Characteristics**:
    - Can be used for both access control and email distribution
    - Members inherit all permissions assigned to the group
    - Enable security principal functionality in AD
- **Use Cases**:
    - Granting access to file shares
    - Assigning application permissions
    - Delegating administrative privileges

### 2. Distribution Groups

- **Primary Purpose**: Email distribution (function like mailing lists)
- **Key Characteristics**:
    - Used exclusively by email applications (e.g., Microsoft Exchange)
    - Cannot be used for security permissions
    - Appear as single recipients in email clients
- **Use Cases**:
    - Department-wide email announcements
    - Project team communications
    - Organization-wide notifications

## Group Scopes

Group
 scope determines where the group can be used and what objects it can 
contain. There are three group scopes in Active Directory:

### 1. Domain Local Groups

- **Membership Can Include**:
    - User accounts from any domain
    - Global groups from any domain
    - Universal groups from any domain
    - Other domain local groups from the same domain
- **Permissions**:
    - Can only be assigned within their own domain
- **Best Practices**:
    - Use for resource access within a single domain
    - Typically contain global groups as members
- **Example Use Case**:
    - Granting access to a department-specific file share

### 2. Global Groups

- **Membership Can Include**:
    - User accounts from the same domain
    - Other global groups from the same domain
- **Permissions**:
    - Can be assigned in any domain in the forest
- **Best Practices**:
    - Use to organize users who share similar roles
    - Often added to domain local groups for resource access
- **Example Use Case**:
    - Creating a group for all marketing department users

### 3. Universal Groups

- **Membership Can Include**:
    - User accounts from any domain
    - Global groups from any domain
    - Other universal groups from any domain
- **Permissions**:
    - Can be assigned in any domain in the forest
- **Best Practices**:
    - Use for forest-wide access needs
    - Prefer adding global groups rather than individual users
    - Minimize membership changes (triggers forest-wide replication)
- **Example Use Case**:
    - Granting access to enterprise-wide applications

## Scope Conversion Rules

Group scopes can be changed with certain restrictions:

| Current Scope | Can Convert To | Restrictions |
| --- | --- | --- |
| Global | Universal | Must not be nested in another global group |
| Domain Local | Universal | Must not contain other domain local groups |
| Universal | Domain Local | No restrictions |
| Universal | Global | Must not contain other universal groups |

## Built-in vs. Custom Groups

### Built-in Groups

- Created automatically during domain installation
- Typically have Domain Local scope
- Used for specific administrative functions
- Examples:
    - Administrators
    - Users
    - Guests
    - Print Operators
    - Backup Operators

### Custom Groups

- Created by administrators for organizational needs
- Can be any type and scope
- Often reflect business structure (departments, projects)
- May be created automatically by applications (e.g., Exchange)

## Nested Group Membership

Nesting refers to adding groups as members of other groups, creating hierarchical permission structures.

### Key Aspects:

- **Inheritance**: Members inherit permissions from all nested groups
- **Complexity**: Can create intricate permission chains
- **Management Challenges**:
    - Difficult to track effective permissions
    - May lead to unintended privilege escalation
- **Security Implications**:
    - Attackers often exploit nested group memberships
    - Tools like BloodHound can visualize nesting relationships

### Example Attack Scenario:

1. Attacker compromises user in "Help Desk" group
2. "Help Desk" is nested in "Helpdesk Level 1"
3. "Helpdesk Level 1" has GenericWrite permission on "Tier 1 Admins"
4. Attacker adds themselves to "Tier 1 Admins"
5. Gains elevated privileges associated with admin group

## Important Group Attributes

| Attribute | Description | Importance |
| --- | --- | --- |
| cn | Common Name of the group | Primary identifier in AD |
| member | List of group members | Shows direct membership |
| groupType | Numeric value indicating type and scope | Determines group functionality |
| memberOf | Groups this group is nested in | Shows indirect permissions |
| objectSid | Security Identifier | Unique security principal identifier |
| distinguishedName | Full LDAP path to object | Used for precise identification |

## Best Practices for Group Management

1. **Follow AGDLP/AGUDLP principles**:
    - Accounts → Global groups → Domain Local groups → Permissions
    - (Optional Universal groups in multi-domain environments)
2. **Regular audits**:
    - Review group memberships
    - Verify permissions are still required
    - Remove unused groups
3. **Documentation**:
    - Maintain records of group purposes
    - Document permission assignments
4. **Nesting strategy**:
    - Limit nesting depth (recommend ≤ 3 levels)
    - Avoid circular nesting
5. **Privilege management**:
    - Use dedicated admin groups
    - Implement just-enough administration
6. **Monitoring**:
    - Track changes to sensitive groups
    - Alert on unexpected membership modifications

## Security Considerations

1. **Over-privileged groups**:
    - Common with built-in groups like Domain Admins
    - Prefer creating custom groups with limited privileges
2. **Permission sprawl**:
    - Users accumulating permissions through multiple groups
    - Regular access reviews help mitigate
3. **Service accounts**:
    - Often added to powerful groups
    - Should have dedicated groups with minimum privileges
4. **Attack surface**:
    - Groups are prime targets for privilege escalation
    - Compromise often leads to lateral movement