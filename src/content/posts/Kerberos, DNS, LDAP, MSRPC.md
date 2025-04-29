---
title: "Kerberos, DNS, LDAP, MSRPC"
published: 2025-04-20
description: "A simplified guide to Kerberos, DNS, LDAP, MSRPC."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

# Kerberos, DNS, LDAP, MSRPC

### **Kerberos**

- **Purpose:** Default authentication protocol in Windows domains (since Windows 2000).
- **Function:** Authenticates users without sending passwords over the network.
- **Process:**
    1. User logs in and sends a request (AS-REQ) encrypted with their password hash.
    2. The **KDC** (part of the Domain Controller) issues a **TGT** (Ticket Granting Ticket).
    3. The TGT is used to request a **TGS** (Ticket Granting Service ticket) to access a specific service.
    4. The TGS is presented to the service, which allows access if valid.
- **Port:** TCP/UDP 88
- **Tool for Recon:** `nmap -p 88` to find KDCs (Domain Controllers)

---

### **DNS**

- **Purpose:** Resolves hostnames ↔ IP addresses and helps clients locate services like Domain Controllers.
- **AD Integration:** Uses **SRV records** to advertise services.
- **Dynamic DNS:** Automatically updates DNS when devices join or change IPs.
- **Ports:** TCP/UDP 53
- **Example Commands:**
    
    ```java
    nslookup INLANEFREIGHT.LOCAL
    nslookup 172.16.6.5
    nslookup ACADEMY-EA-DC01
    ```
    

### **LDAP (Lightweight Directory Access Protocol)**

- **Purpose:** Protocol used by AD to store and retrieve directory information (users, groups, etc.).
- **Ports:**
    - **LDAP:** TCP 389
    - **LDAPS (secure):** TCP 636
- **Auth Modes:**
    1. **Simple** (username/password)
    2. **SASL** (e.g., Kerberos-backed secure method)
- **Vulnerability Note:** Transmits credentials in **plaintext** by default—use TLS.
- **Analogy:** AD is like Apache, and LDAP is like HTTP.

---

### **MSRPC (Microsoft Remote Procedure Call)**

- **Purpose:** Enables remote client-server interaction for Windows services.
- **Interfaces Used in AD:**
    - **lsarpc:** Manages local/domain security policy.
    - **netlogon:** Authenticates users and services in the domain.
    - **samr:** Manages domain users and groups; used in reconnaissance (e.g., BloodHound).
    - **drsuapi:** Handles replication between Domain Controllers; can be abused to dump `NTDS.dit` (user hashes).
- **Security Concern:** Misconfigurations can allow **reconnaissance** or **hash theft** for lateral movement or domain takeover.

---

### Summary Table

| Protocol | Port(s) | Purpose |
| --- | --- | --- |
| **Kerberos** | TCP/UDP 88 | Secure authentication using tickets |
| **DNS** | TCP/UDP 53 | Name resolution and locating services |
| **LDAP** | TCP 389 / 636 | Accessing and managing directory info |
| **MSRPC** | Dynamic ports | Remote management and service access |