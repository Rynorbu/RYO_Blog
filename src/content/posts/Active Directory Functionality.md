---
title: "Active Directory Functionality"
published: 2025-04-20
description: "A simplified guide to Active Directory functionality."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

# Active Directory Functionality

## **1. FSMO Roles (Flexible Single Master Operations)**

Active Directory is **multi-master**, meaning any Domain Controller (DC) can make changes. However, to prevent conflicts, some **operations are handled by a single DC** — these are called FSMO roles. There are **five FSMO roles**, divided into:

### *Forest-wide roles* (1 copy across the forest):

1. **Schema Master**
    - Controls **updates and changes to the AD schema**.
    - Schema = defines the **structure of objects** (e.g., user has attributes like `name`, `email`, `password`, etc.).
    - Only one Schema Master per **forest**.
    - Example: When you install an application that extends the schema (like Exchange), it contacts the Schema Master.
2. **Domain Naming Master**
    - Responsible for **adding/removing domains** in the forest.
    - Ensures **no duplicate domain names**.
    - Only one per **forest**.

---

### *Domain-wide roles* (1 copy per domain):

1. **Relative ID (RID) Master**
    - Every object in AD has a unique **Security Identifier (SID)**.
    - SID = `Domain SID + RID`.
    - The RID Master hands out **blocks of RIDs** to other DCs so they can assign SIDs locally.
    - Prevents **duplicate SIDs**.
2. **PDC Emulator (Primary Domain Controller)**
    - Acts as the **main DC** for compatibility with older systems (like NT4).
    - Handles:
        - **Password changes** (authoritative source)
        - **Time synchronization**
        - **Group Policy updates**
        - **Account lockouts**
    - Also used in **Kerberos authentication**.
3. **Infrastructure Master**
    - Translates **GUIDs, SIDs, and DNs** (distinguished names) across domains.
    - Used when **users from one domain access resources in another**.
    - Helps resolve security identifiers (SIDs) to user names.
    - If not working properly, ACLs show **raw SIDs** instead of names.

---

### Why FSMO Roles Matter:

If any FSMO role **fails or is not reachable**, you might face:

- Login/authentication failures
- Group policy misbehavior
- SID resolution issues

---

## **2. Domain and Forest Functional Levels**

These determine **what AD features are available** and which **Windows Server versions** are supported.

---

### 🔸 **Domain Functional Level (DFL)**

Each domain can have its own level. Here’s what each level provides:

| Domain Functional Level | Key Features | Supported DC OS |
| --- | --- | --- |
| **Windows 2000 native** | Group nesting, SID history | Windows 2000 – 2008 R2 |
| **Windows Server 2003** | `lastLogonTimestamp`, `netdom`, selective auth | Windows 2003 – 2012 R2 |
| **Windows Server 2008** | DFS-R, AES for Kerberos, Fine-grained Password Policies | 2008 – 2012 R2 |
| **Windows Server 2008 R2** | Managed Service Accounts, Authentication mechanism assurance | 2008 R2+ |
| **Windows Server 2012** | Claims-based auth, Kerberos armoring | 2012+ |
| **Windows Server 2012 R2** | Protected Users, Authentication Policies & Silos | 2012 R2 |
| **Windows Server 2016** | Credential protection, Smartcard enforcement | 2016+ |
|  |  |  |

⚠️ No new DFL with **Windows Server 2019**, but minimum required DFL for 2019 is **2008**, and SYSVOL must use **DFS-R** replication.

### 🔸 **Forest Functional Level (FFL)**

Applies to **entire forest**. Limits the oldest OS version allowed and unlocks forest-wide features.

| Forest Functional Level | Key Capabilities |
| --- | --- |
| **2003** | Forest trusts, Domain rename, RODC |
| **2008** | No new forest-wide features |
| **2008 R2** | Active Directory Recycle Bin (undelete AD objects) |
| **2012 / 2012 R2** | No new features |
| **2016** | PAM (Privileged Access Management) via Microsoft Identity Manager |

> Recycle Bin and PAM are the most notable improvements.
> 

---

## **3. Trusts in Active Directory**

Trusts allow users from **one domain** to access resources in **another domain or forest**. They connect the **authentication systems** of different domains.

---

### **Types of Trusts**:

| Trust Type | Description |
| --- | --- |
| **Parent-child** | Automatic trust in same forest; bidirectional and transitive |
| **Cross-link** | Manually created between domains in different trees (same forest); speeds up auth |
| **External** | Between two separate domains in different forests; **non-transitive** |
| **Tree-root** | Trust between root domains of separate domain trees in the same forest |
| **Forest** | Transitive trust between **root domains** of two separate **forests** |

### **Trust Properties**:

- **Transitive Trust**:
    - Trust extends beyond two domains.
    - Example: A trusts B, B trusts C → A can trust C.
- **Non-Transitive Trust**:
    - Trust limited to the two domains only.
- **One-Way Trust**:
    - Domain A trusts B → Users in B can access A, but not vice-versa.
- **Two-Way Trust**:
    - Both domains trust each other → Users in both can access each other’s resources.

---

### **Security Risks with Trusts**:

- **Improperly configured trusts** can create attack paths.
- Example: If a user in a trusted domain gets compromised, attacker might gain access to the principal domain.
- **Kerberoasting**: An attacker could request service tickets for accounts in another domain and crack them offline.
- **SID Filtering** helps limit this, but not always configured properly.

---

### Summary

| Component | Purpose |
| --- | --- |
| **FSMO Roles** | Critical AD functions assigned to specific Domain Controllers |
| **Functional Levels** | Define AD features & OS compatibility |
| **Trusts** | Allow resource sharing between domains/forests |