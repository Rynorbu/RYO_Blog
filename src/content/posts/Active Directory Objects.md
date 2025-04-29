---
title: "Active Directory Objects"
published: 2025-04-20
description: "A simplified guide to Active Directory Objects."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---


# Active Directory Objects

### **What is an Active Directory Object?**

In **Active Directory**, an **object** is **any resource** within the AD environment. This includes:

- **Users**
- **Computers**
- **Printers**
- **Groups**
- **Organizational Units (OUs)**
- **Shared folders**
- **Domains**
- **Domain Controllers**, and more.

Each object has a unique identity and a set of attributes. Some objects can **contain other objects** (these are called *container objects*), while others cannot (*leaf objects*).

---

### **Types of AD Objects**

---

### **1. Users**

- **Definition**: Represent individual accounts for people in the organization.
- **Type**: Leaf object (cannot contain other objects).
- **Identifiers**:
    - **SID (Security Identifier)**: Used to manage permissions and access.
    - **GUID (Globally Unique Identifier)**: Uniquely identifies the object across domains and forests.
- **Attributes**: Name, email, login time, password details, department, manager, address, etc.
- **Security Principal**: Yes. Users can log in and be granted permissions.
- **Security Risk**: Compromising even a low-privileged user account can allow attackers to access other resources or enumerate the AD environment.

---

### **2. Contacts**

- **Definition**: Represent **external people** (e.g., vendors, customers) without login privileges.
- **Type**: Leaf object.
- **Security Principal**: ❌ No. Cannot log in or be granted permissions.
- **Identifiers**: Only a GUID.
- **Attributes**: First name, last name, email, phone number, etc.

---

### **3. Printers**

- **Definition**: Point to a printer accessible via AD.
- **Type**: Leaf object.
- **Security Principal**: ❌ No.
- **Identifiers**: Only a GUID.
- **Attributes**: Printer name, driver, port info, etc.

---

### **4. Computers**

- **Definition**: Any machine (workstation/server) joined to the AD.
- **Type**: Leaf object.
- **Security Principal**: ✅ Yes.
- **Identifiers**: SID and GUID.
- **Purpose**: Like users, computer accounts can authenticate and access network resources.
- **Security Concern**: If attackers gain **NT AUTHORITY\SYSTEM** access, they can perform many tasks like a regular user.

---

### **5. Shared Folders**

- **Definition**: Points to folders on networked computers shared within the AD environment.
- **Type**: Leaf object.
- **Security Principal**: ❌ No.
- **Identifiers**: Only a GUID.
- **Access Control**:
    - Open to all
    - Only authenticated users
    - Specific groups/users only
- **Attributes**: Folder name, path, access permissions.

---

### **6. Groups**

- **Definition**: A **container object** used to manage multiple users/computers together.
- **Type**: Container object.
- **Security Principal**: ✅ Yes.
- **Identifiers**: SID and GUID.
- **Use Cases**:
    - Assign permissions collectively (e.g., give helpdesk access to all members).
    - Control software access, policy application, etc.
- **Nested Groups**:
    - Groups can be members of other groups.
    - Can lead to **privilege escalation** or **misconfigured access**.
- **Tool**: **BloodHound** is commonly used to visualize group relationships and discover attack paths.

---

### **7. Organizational Units (OUs)**

- **Definition**: **Container objects** used to logically group AD objects for easier administration.
- **Purpose**:
    - Delegate specific administrative rights (e.g., reset passwords only for users in Marketing OU).
    - Apply **Group Policy Objects (GPOs)** to control settings and enforce security.
- **Structure**:
    - Can have **parent-child hierarchies** (e.g., Employees ➝ Marketing, HR, IT).
- **Attributes**: Name, members, linked policies, security settings.

---

### **8. Domain**

- **Definition**: The **core boundary** of an AD environment; defines a unique namespace and holds all objects.
- **Purpose**:
    - Each domain has its **own database**, **policies**, and **security boundary**.
    - Policies can control password complexity, access to apps, drive mappings, etc.
- **Examples of policies**:
    - Enforce complex passwords
    - Disable command prompt for non-admins
    - Auto-map network drives

---

### **9. Domain Controllers (DCs)**

- **Definition**: Servers that **authenticate users** and enforce security policies.
- **Functions**:
    - Validate login requests
    - Store the entire **Active Directory database**
    - Replicate AD changes across the network
- **Crucial Role**: If DCs are down, users cannot authenticate, and network services fail.

---

### **10. Sites**

- **Definition**: Grouping of computers based on **IP subnets** and **network proximity**.
- **Purpose**:
    - Optimize **replication traffic** between DCs.
    - Improve login speed by directing clients to the closest DC.

---

### **11. Built-in**

- **Definition**: A **default container** created automatically when a domain is set up.
- **Contains**:
    - Default groups like Administrators, Backup Operators, Users, etc.
    - These have predefined rights and permissions.

---

### **12. Foreign Security Principals (FSPs)**

- **Definition**: Placeholder objects in AD for users or groups from **trusted external forests**.
- **Scenario**:
    - If you add a user from another domain/forest to a group in your domain, an FSP is created.
- **Purpose**:
    - Store the **foreign SID** to ensure permissions work correctly via **trust relationships**.
- **Location**:
    - Stored in the container:
        
        `CN=ForeignSecurityPrincipals,DC=yourdomain,DC=com`
        

---

### Summary Table

| Object Type | Container | Security Principal | SID | GUID | Common Use |
| --- | --- | --- | --- | --- | --- |
| User | ❌ | ✅ | ✅ | ✅ | Login and access |
| Contact | ❌ | ❌ | ❌ | ✅ | Info-only external entity |
| Printer | ❌ | ❌ | ❌ | ✅ | Networked printing |
| Computer | ❌ | ✅ | ✅ | ✅ | Workstation/server object |
| Shared Folder | ❌ | ❌ | ❌ | ✅ | Access-controlled file storage |
| Group | ✅ | ✅ | ✅ | ✅ | Access control, nesting |
| Organizational Unit (OU) | ✅ | ❌ | ❌ | ✅ | Delegation & policy grouping |
| Domain | ✅ | ✅ | ✅ | ✅ | AD structure with unique DB |
| Domain Controller | ✅ | ✅ | ✅ | ✅ | Authenticator and policy enforcer |
| Site | ✅ | ❌ | ❌ | ✅ | Efficient replication |
| Built-in | ✅ | ✅ | ✅ | ✅ | Default groups |
| Foreign Security Principal | ❌ | ✅ (external) | ✅ | ✅ | External forest mapping |