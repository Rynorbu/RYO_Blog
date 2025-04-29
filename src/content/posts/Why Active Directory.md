---
title: "Why Active Directory"
published: 2025-04-20
description: "A simplified guide to Why Active Directory."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

# Why Active Directory?

## What is Active Directory (AD)?

**Active Directory** is a **system created by Microsoft** that helps **manage and organize** everything in a **Windows-based network**—like users, computers, printers, files, and more. Think of it as a **giant phone book** or **directory** for an organization, but instead of just names and numbers, it stores a lot more information about resources and who can access them.

**Why is AD Important?**

---

- AD helps **network administrators** manage permissions and access to resources across the organization.
- It keeps track of **who is who** (users), **what is what** (devices), and **who can do what** (permissions).
- AD is very common—**95% of Fortune 500 companies** use it, so it's everywhere.

---

**How is AD Organized?**

Active Directory is structured **hierarchically**, which means it’s like a **family tree** or **nested folders**:

1. **Domain** – Like a neighborhood or organization. It contains users, computers, and other resources.
2. **Organizational Units (OUs)** – Like folders within the domain, used to organize users/devices.
3. **Forest** – A collection of one or more domains.
4. **Tree** – A set of domains connected together.

---

**What Can You Do With AD?**

You can:

- **Log in** to any computer in the network with one account.
- **Assign roles and permissions** to users (who can access what).
- Apply **Group Policies** (like rules for computers/users).
- **Share files** and **connect to printers** easily.
- **Track and audit** activities for security.

---

### Why is AD Targeted by Hackers?

- AD is **very powerful**—if you control it, you control the entire network.
- It's **backward-compatible**, meaning it keeps old features for older systems, which can introduce **security holes**.
- Many features are **not secure by default**, so if it's not set up properly, it's vulnerable.

---

### Any User Can See A Lot

Even a **basic user with no special permissions** can see **a huge amount of information** in AD. That’s because:

- AD is like a **read-only book** everyone can open.
- Users can look around and **enumerate (list)** details like:
    - Users
    - Computers
    - Groups
    - Permissions

This makes it easier for attackers to **map out the network** and look for ways to break in deeper.

---

### How Do We Defend Against AD Attacks?

To protect AD, organizations must:

- **Set up proper configurations** and remove defaults.
- Use **Defense in Depth** – multiple layers of security.
- Apply **Least Privilege** – users only get the access they need.
- **Patch regularly** – fix known security issues quickly.
- Use **network segmentation** – break the network into parts to limit attacker movement.

---

### Real Attacks That Exploited AD

### Notable Attacks:

1. **noPac (2021)** – An attacker can get control of an entire domain using just a normal user account.
2. **PrintNightmare (CVE-2021-34527)** – Abuse of the Windows print service to get admin access.
3. **Zerologon (CVE-2020-1472)** – A flaw that lets attackers reset passwords and take over domain controllers.
4. **Conti Ransomware** – A ransomware that used AD vulnerabilities to spread fast and cause damage.

These show how dangerous AD flaws can be if not fixed.

---

### What Tools Help Hackers and Defenders?

There are many **tools** available to **scan and test** Active Directory. These tools can:

- Find misconfigurations
- Show relationships between users and groups
- Test for vulnerabilities
But remember: tools are only useful if the person using them **understands how AD works**.

---

History of Active Directory

- **1971** – LDAP (a core protocol used by AD) was introduced.
- **1990** – Microsoft tried to build directory services in Windows NT 3.0.
- **2000** – Active Directory became part of Windows Server 2000.
- **2003** – Introduced **Forest** for managing multiple domains.
- **2008** – Introduced **AD Federation Services (ADFS)** for Single Sign-On.
- **2016** – Introduced cloud features, better monitoring, and **gMSA** for secure service accounts.

### What's ADFS?

ADFS helps users **log into different applications** (even from other organizations) using the **same account**. It uses **claims** (like "this person is a manager") to manage access.

### What's gMSA?

Group Managed Service Accounts – allow secure, automated password management for services. They help protect against attacks like **Kerberoasting**, where attackers steal service account passwords.

---

What About the Cloud?

- **Azure AD Connect** helps sync on-prem AD with **Microsoft Office 365** and other cloud services.
- Even with the cloud, **on-prem AD is still widely used**, and you’ll find it almost everywhere in real-world networks.

---

Why Is Learning AD Important?

As a **security professional**:

- You’ll definitely run into AD during your career.
- If you're doing **pen testing**, AD is a common target.
- Understanding AD helps you **attack and defend** more effectively.
- Knowing the structure helps you **spot flaws**, explain them to clients, and suggest fixes.

---

## Summary in Simple Points:

- AD is a system that helps manage users, devices, and permissions in a network.
- Every user can see a lot of data in AD, so it must be secured properly.
- Many dangerous attacks start with just a normal user account in AD.
- Tools exist to explore and attack AD, but you need to **understand AD** to use them well.
- If you want to be a great cybersecurity pro, you **must learn AD fundamentals**.
- AD is not going away, even with the rise of cloud systems.