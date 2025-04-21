---
title: Introduction
published: 2025-04-21
description: "A introduction to web Information Gathering Information Gathering"
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

## Web Reconnaissance: The First Step in Penetration Testing

Web reconnaissance is the **foundation of any thorough security assessment**. It involves collecting critical information about a target website or web application before diving into deeper analysis or exploitation. Think of it as digital detective work—it helps identify weaknesses and entry points **before** any attack is even launched.

### Objectives of Web Reconnaissance

1. **Identifying Assets**
    - Discover web pages, subdomains, IPs, and technologies used.
    - Gain a full picture of the target's online presence.
2. **Discovering Hidden Information**
    - Locate exposed backup/config files or internal documents.
    - These can unintentionally leak sensitive data.
3. **Analysing the Attack Surface**
    - Spot vulnerabilities by understanding tech stacks and misconfigurations.
4. **Gathering Intelligence**
    - Collect info that can aid in further exploitation (e.g., employee names, emails).

Both attackers and defenders use reconnaissance:

- Attackers to **find weaknesses**.
- Defenders to **fix them first**.

### Types of Reconnaissance

Recon is typically divided into **two categories**:

### Active Reconnaissance

This involves **direct interaction** with the target—more effective but riskier as it may trigger alerts.

| Technique               | Description                                   | Example Tools          | Risk            |
|-------------------------|-----------------------------------------------|------------------------|-----------------|
| Port Scanning          | Finds open ports and running services         | Nmap, Masscan          | 🔺 High         |
| Vulnerability Scanning | Probes for known flaws (e.g., XSS, SQLi)     | Nessus, Nikto          | 🔺 High         |
| Network Mapping        | Maps target’s network path                    | Traceroute, Nmap       | 🔺 Medium–High  |
| Banner Grabbing        | Reads service banners for software/version   | Netcat, curl           | 🔸 Low          |
| OS Fingerprinting      | Detects the target’s operating system         | Nmap (-O), Xprobe2     | 🔸 Low          |
| Service Enumeration    | Identifies exact versions of services         | Nmap (-sV)             | 🔸 Low          |
| Web Spidering         | Crawls website for structure, hidden files    | Burp Suite Spider, OWASP ZAP | 🔸 Medium |


**Pros**: Detailed info

**Cons**: High chance of detection


### Passive Reconnaissance

This technique gathers information **without touching the target directly**. It's stealthy but may yield limited data.

| Technique               | Description                          | Example Tools         | Risk        |
|-------------------------|--------------------------------------|-----------------------|-------------|
| Search Engine Queries   | Google-fu to find data on target     | Google, Shodan        | Very Low |
| WHOIS Lookups           | Reveals domain ownership info        | WHOIS tools           | Very Low |
| DNS Analysis            | Identifies subdomains and mail servers | dig, dnsrecon        | Very Low |
| Web Archive Analysis    | Examines old versions of sites       | Wayback Machine       | Very Low |
| Social Media Analysis   | OSINT via LinkedIn, Twitter, etc.    | LinkedIn, Facebook    | Very Low |
| Code Repositories       | Checks GitHub for exposed code or secrets | GitHub, GitLab | Very Low |


**Pros**: Undetectable

**Cons**: Limited to publicly available data

### Starting Point: WHOIS

The **WHOIS protocol** helps identify:

- Domain registrant details
- Contact info
- Name servers
- Expiry and creation dates

Understanding WHOIS gives you a good grasp of **who owns the domain and how it’s structured**, making it a crucial first step in reconnaissance.


### Summary

- **Web reconnaissance = digital footprint discovery**
- It is vital for both attackers (to exploit) and defenders (to secure)
- Comes in two flavors: **Active (direct, riskier)** and **Passive (indirect, stealthy)**
- Tools like **Nmap, WHOIS, Shodan, Burp Suite, and GitHub** play a key role
- Mastering recon sets the stage for all further penetration testing phases