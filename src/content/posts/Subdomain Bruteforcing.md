---
title: Subdomain Bruteforcing
published: 2025-04-21
description: "A simplified guide to subdomains in web development."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

### Subdomain Bruteforcing – Active Enumeration Technique

**Subdomain brute-force enumeration** is a methodical approach to uncovering hidden or lesser-known subdomains by appending potential names from a wordlist to a target domain and checking if they resolve via DNS.

### Key Steps in the Bruteforce Process

1. **Wordlist Selection**
    
    Choose the right wordlist for your target:
    
    - **General-Purpose**: Covers common names like **admin**, **dev**, **test**, **mail**.
    - **Targeted**: Tailored to specific industries or known patterns.
    - **Custom**: Crafted from recon intel, breach data, etc.
2. **Iteration & Querying**
    
    Tools systematically generate combinations like:
    
    ```jsx
    dev.example.com
    staging.example.com
    test.example.com
    ```
    
3. **DNS Lookup**
    
    Each subdomain is checked to see if it resolves (usually via A/AAAA records).
    
4. **Filtering & Validation**
    
    Valid entries (resolving subdomains) are collected. Optional steps:
    
    - Web request probing
    - Port scanning
    - Screenshotting (with tools like Aquatone or EyeWitness)

### Tools for Subdomain Brute-Forcing

| Tool         | Description |
|--------------|-------------|
| dnsenum    | Perl-based tool with brute-forcing, zone transfer, Google scraping, WHOIS, and reverse lookups. |
| fierce     | Recursive subdomain discovery with wildcard detection. |
| dnsrecon   | Multi-technique DNS recon with customizable output. |
| amass      | Powerful recon platform with passive/active modes and data source integration. |
| assetfinder| Lightweight subdomain finder for quick scans. |
| puredns    | High-speed brute-forcing tool with mass resolver support and wildcard filtering. |

### Tool in Action: Using `dnsenum`

We’ll perform enumeration on the domain inlanefreight.com using a top 5000 wordlist:

```jsx
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

**Optional recursive brute-force:**

```jsx
dnsenum --enum inlanefreight.com -f /path/to/wordlist.txt -r
```

### Sample Output

```jsx
----- inlanefreight.com -----
Host's addresses:
inlanefreight.com. 300 IN A 134.209.24.248

Brute forcing with subdomains-top1million-5000.txt:
www.inlanefreight.com. 300 IN A 134.209.24.248
support.inlanefreight.com. 300 IN A 134.209.24.248
```

### Tips for Better Results

- Always check for **wildcard DNS** responses to avoid false positives.
- Use tools like **massdns**, **puredns**, or **dnsx** for **high-performance enumeration**.
- Combine **passive subdomain enumeration** (e.g., `crt.sh`, `VirusTotal`) with brute-force for full coverage.