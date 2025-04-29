---
title: "Active Directory Terminology"
published: 2025-04-20
description: "A simplified guide to Active Directory Terminology."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

## **1. Core Components**

### **1.1. Object**

- **Definition**: Any resource within Active Directory (AD), such as users, computers, printers, OUs, and domain controllers.
- **Examples**:
    - User: `bjones`
    - Computer: `WS01`
    - Organizational Unit (OU): `IT Department`

### **1.2. Attributes**

- **Definition**: Characteristics that define an object.
- **Examples**:
    - User attributes: `givenName` (First Name), `sn` (Surname), `mail` (Email)
    - Computer attributes: `dNSHostName`, `operatingSystem`
- **LDAP Names**: Used in queries (e.g., `displayName` for "Full Name").

### **1.3. Schema**

- **Definition**: The blueprint of AD, defining object types and their attributes.
- **Key Points**:
    - Defines classes (e.g., `user`, `computer`).
    - Each object is an **instance** of a class (e.g., `RDS01` is an instance of the `computer` class).
    - **Instantiation**: Creating an object from a class.

---

## **2. Hierarchical Structure**

### **2.1. Domain**

- **Definition**: A logical group of AD objects (users, computers, OUs).
- **Analogy**: Like a city within a state.
- **Key Points**:
    - Domains can be independent or connected via **trust relationships**.

### **2.2. Forest**

- **Definition**: The top-level container holding all AD objects.
- **Analogy**: Like a country or state.
- **Key Points**:
    - Contains **one or more domains**.
    - Operates independently but may have **cross-forest trusts**.

### **2.3. Tree**

- **Definition**: A collection of domains under a single root domain.
- **Example**:
    - Root: `inlanefreight.local`
    - Child: `corp.inlanefreight.local`
- **Key Points**:
    - Domains in a tree share a **Global Catalog**.
    - **Parent-child trust** relationships exist.

### **2.4. Organizational Unit (OU)**

- **Definition**: A container for organizing objects within a domain.
- **Purpose**: Used for applying **Group Policies (GPOs)** and delegation.

---

## **3. Identifiers & Security**

### **3.1. Global Unique Identifier (GUID)**

- **Definition**: A **128-bit unique ID** assigned to every AD object.
- **Key Points**:
    - Stored in `ObjectGUID`.
    - Never changes, even if the object is moved.

### **3.2. Security Identifier (SID)**

- **Definition**: A unique ID for security principals (users, groups, computers).
- **Format**: `S-1-5-21-3623811015-3361044348-30300820-1013`
- **Key Points**:
    - Used in **access tokens**.
    - **Well-known SIDs** (e.g., `S-1-1-0` = "Everyone" group).

### **3.3. Distinguished Name (DN)**

- **Definition**: The full path to an object in AD.
- **Example**:
    
    `cn=bjones,ou=IT,ou=Employees,dc=inlanefreight,dc=local`
    

### **3.4. Relative Distinguished Name (RDN)**

- **Definition**: The unique name at a given level in the hierarchy.
- **Example**:
    
    In `cn=bjones,ou=IT`, `bjones` is the RDN.
    

### **3.5. sAMAccountName**

- **Definition**: The **logon name** (e.g., `bjones`).
- **Limitation**: Must be **unique** and ≤ **20 characters**.

### **3.6. User Principal Name (UPN)**

- **Definition**: An alternate login format (e.g., `bjones@inlanefreight.local`).

---

## **4. Domain Controllers & Replication**

### **4.1. FSMO Roles**

- **Definition**: **Flexible Single Master Operation** roles for AD management.
- **Five Roles**:
    1. **Schema Master** (Forest-wide) – Manages schema changes.
    2. **Domain Naming Master** (Forest-wide) – Handles domain additions.
    3. **RID Master** (Per-domain) – Allocates **Relative IDs** for SIDs.
    4. **PDC Emulator** (Per-domain) – Manages password changes & time sync.
    5. **Infrastructure Master** (Per-domain) – Updates cross-domain references.

### **4.2. Global Catalog (GC)**

- **Definition**: A DC that stores **partial copies** of all objects in a forest.
- **Functions**:
    - Enables **cross-domain searches**.
    - Assists in **authentication**.

### **4.3. Read-Only Domain Controller (RODC)**

- **Definition**: A **non-writable** DC for branch offices.
- **Security Features**:
    - No password caching (except its own).
    - Prevents malicious replication.

### **4.4. Replication**

- **Definition**: Synchronizing AD changes across DCs.
- **Process**:
    - **KCC (Knowledge Consistency Checker)** manages replication paths.
    - Ensures **high availability**.

---

## **5. Security & Access Control**

### **5.1. Security Principals**

- **Definition**: Objects that can be authenticated (users, computers, services).
- **Example**: A **service account** running `Tomcat`.

### **5.2. Access Control Lists (ACLs)**

- **Definition**: Defines **who can access an object**.
- **Components**:
    - **DACL (Discretionary ACL)** – Grants/denies access.
    - **SACL (System ACL)** – Logs access attempts.
    - **ACE (Access Control Entry)** – Individual permissions.

### **5.3. AdminSDHolder**

- **Definition**: Protects **privileged groups** (e.g., Domain Admins).
- **SDProp Process**:
    - Runs **hourly** on the **PDC Emulator**.
    - Resets ACLs for protected groups.

### **5.4. adminCount Attribute**

- **Definition**: Indicates if a user is **protected by AdminSDHolder**.
- **Values**:
    - `1` = Protected.
    - `0` = Not protected.

---

## **6. Additional Concepts**

### **6.1. Service Principal Name (SPN)**

- **Definition**: Unique identifier for a **service** in Kerberos.
- **Example**: `HTTP/webserver.inlanefreight.local`

### **6.2. Group Policy Object (GPO)**

- **Definition**: A policy setting applied to users/computers.
- **Stored in**: `SYSVOL` (replicated across DCs).

### **6.3. NTDS.DIT**

- **Definition**: The **AD database file** containing:
    - User accounts.
    - **Password hashes** (NTLM, Kerberos).
    - Group memberships.
- **Location**: `C:\Windows\NTDS\NTDS.DIT`

### **6.4. Tombstone & AD Recycle Bin**

- **Tombstone**:
    - A **deleted object** retained for **60-180 days**.
    - `isDeleted` flag set to `TRUE`.
- **AD Recycle Bin**:
    - Allows **full recovery** of deleted objects.
    - Preserves **most attributes**.

### **6.5. SYSVOL**

- **Definition**: A shared folder storing:
    - **GPOs**.
    - **Logon scripts**.
    - **File replication data**.

### **6.6. sIDHistory**

- **Definition**: Stores **previous SIDs** after migration.
- **Security Risk**: Can be abused for **privilege escalation**.

### **6.7. MSBROWSE (Legacy)**

- **Definition**: Old protocol for **network browsing**.
- **Replaced by**: **SMB/CIFS**.

---

## **7. Tools**

- **ADUC (Active Directory Users & Computers)** – GUI for managing AD.
- **ADSI Edit** – Advanced AD object editor.
- **PowerShell** – Scripted AD management.

---

## **Conclusion**

Understanding these terms is **critical** for:

- **AD administration**.
- **Security auditing**.
- **Penetration testing** (e.g., exploiting `sIDHistory`, dumping `NTDS.DIT`).