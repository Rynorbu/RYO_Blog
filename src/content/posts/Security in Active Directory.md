---
title: "Security in Active Directory"
published: 2025-04-20
description: "A simplified guide to Security in Active Directory."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

# Security in Active Directory

Active Directory (AD) is a critical 
component of enterprise IT infrastructure, providing centralized 
authentication, authorization, and management. However, its default 
configuration prioritizes **availability and confidentiality** over **integrity**, making it vulnerable to attacks if not properly secured.

This guide explores **essential security hardening measures** for Active Directory, organized into key categories:

1. **General Hardening Measures**
2. **Account Security & Password Policies**
3. **Group Policy & Access Control**
4. **Logging, Monitoring & Auditing**
5. **Service & Administrative Account Security**
6. **Patch & Update Management**
7. **Best Practices for Reducing Attack Surface**

---

## **1. General Hardening Measures**

### **Microsoft Local Administrator Password Solution (LAPS)**

- **Purpose**: Randomizes and rotates local admin passwords on Windows hosts to prevent lateral movement.
- **Benefits**:
    - Prevents **pass-the-hash** attacks by ensuring unique passwords per machine.
    - Reduces risk of attackers reusing credentials across multiple systems.
- **Implementation**:
    - Install LAPS via Group Policy.
    - Set password rotation intervals (e.g., every 12-24 hours).
    - Restrict access to LAPS passwords to authorized admins only.

### **Group Managed Service Accounts (gMSA)**

- **Purpose**: Automates password management for service accounts.
- **Benefits**:
    - Uses **120-character passwords** rotated automatically.
    - Eliminates manual password management.
    - Supports multiple hosts (unlike traditional Managed Service Accounts).
- **Implementation**:
    - Create gMSA accounts in AD.
    - Assign them to services/applications instead of regular user accounts.

---

## **2. Account Security & Password Policies**

### **Password Complexity & Passphrases**

- **Best Practices**:
    - **Minimum 12+ characters** (longer for admins).
    - **Enforce passphrases** (e.g., `CorrectHorseBatteryStaple`) instead of complex but short passwords (`P@ssw0rd1`).
    - **Block weak passwords** (e.g., `Welcome1`, `Summer2024`) using password filters.
    - **Enable Multi-Factor Authentication (MFA)** for:
        - Remote Desktop (RDP) access.
        - Privileged account logins.

### **Account Separation (Least Privilege)**

- **Best Practices**:
    - **Separate admin and standard accounts** (e.g., `sjones` for email, `sjones_adm` for admin tasks).
    - **Never log in to workstations with Domain Admin accounts** (only use them on Domain Controllers).
    - **Disable or remove stale accounts** (old service accounts, ex-employee accounts).

### **Kerberos Hardening**

- **Best Practices**:
    - **Reduce Kerberos ticket lifetime** (default: 10 hours) to limit golden ticket attacks.
    - **Enable AES encryption** (disable RC4-HMAC for Kerberos).
    - **Implement Kerberos armoring (FAST)** to prevent brute-force attacks.

---

## **3. Group Policy & Access Control**

### **Security Policies via GPOs**

- **Key Policies to Configure**:
    - **Account Policies**:
        - Password history (24+ remembered passwords).
        - Account lockout threshold (5-10 failed attempts).
        - Minimum password age (1-2 days to prevent rapid changes).
    - **Local Policies**:
        - Disable Guest & built-in Administrator accounts.
        - Rename default admin accounts.
        - Restrict removable media access.
    - **Software Restriction Policies**:
        - Block unauthorized executables (e.g., `cmd.exe`, `powershell.exe` for non-admins).
        - Use **AppLocker** to whitelist approved applications.
    - **User Rights Assignments**:
        - Restrict **"Debug Programs"**, **"Load Drivers"**, and **"Logon Locally"** to admins only.

### **Restricted Groups**

- **Purpose**: Enforce group membership via GPO.
- **Best Practices**:
    - Restrict **Local Administrators** group to only necessary users.
    - Limit **Domain Admins**, **Enterprise Admins**, and **Schema Admins** membership.
    - Remove unnecessary users from **Backup Operators**, **Account Operators**, etc.

---

## **4. Logging, Monitoring & Auditing**

### **Audit Policy Settings**

- **Critical Events to Audit**:
    - **Account Management** (user creation, group changes).
    - **Logon/Logoff Events** (failed logins, suspicious logins).
    - **Object Access** (sensitive file/folder modifications).
    - **Privilege Use** (who is using admin rights?).
    - **Policy Changes** (GPO modifications).

### **SIEM Integration**

- **Best Practices**:
    - Forward logs to a **SIEM (Splunk, ELK, Microsoft Sentinel)**.
    - Set alerts for:
        - **Brute-force attacks** (many failed logins).
        - **Kerberoasting attempts** (excessive TGS requests).
        - **Golden Ticket usage** (unusual Kerberos activity).

---

## **5. Service & Administrative Account Security**

### **Privileged Access Workstations (PAWs)**

- **Best Practices**:
    - Use **dedicated admin workstations** for Domain Admins.
    - Block internet access on PAWs to prevent credential theft.
    - Enforce **MFA for all admin logins**.

### **Just-In-Time (JIT) Admin Access**

- **Best Practices**:
    - Use **Microsoft PIM (Privileged Identity Management)**.
    - Grant admin rights **only when needed** (time-bound access).

---

## **6. Patch & Update Management**

### **WSUS & SCCM**

- **Best Practices**:
    - Use **Windows Server Update Services (WSUS)** for centralized patching.
    - **System Center Configuration Manager (SCCM)** for advanced patch deployment.
    - **Test patches in staging before production rollout**.
    - **Prioritize critical patches** (e.g., Zero-Day vulnerabilities).

---

## **7. Reducing Attack Surface**

### **Network Segmentation**

- **Best Practices**:
    - **Isolate Domain Controllers** (block unnecessary traffic).
    - **Segment admin networks** (separate from user VLANs).
    - **Disable SMBv1** (vulnerable to EternalBlue).

### **Limiting RDP & Local Admin Rights**

- **Best Practices**:
    - **Restrict RDP access** to only necessary users.
    - **Avoid granting local admin rights to Domain Users**.
    - Use **Restricted Groups** to enforce least privilege.

### **Role Separation**

- **Best Practices**:
    - **Do not install IIS, Exchange, or SQL on Domain Controllers**.
    - **Separate web servers, DB servers, and DCs**.
    - **Disable unnecessary services** (e.g., NetBIOS, LLMNR).

---

## **Conclusion**

Active Directory is a powerful tool but **insecure by default**. Organizations must implement **defense-in-depth** strategies, including:

- **Strong password policies & MFA**
- **Restricted admin access & account separation**
- **Logging, monitoring, and SIEM integration**
- **Regular patching & GPO hardening**
- **Network segmentation & least privilege enforceme**