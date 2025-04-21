---
title: DNS
published: 2025-04-21
description: "A guide to DNS techniques and best practices."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

### **What is DNS (Domain Name System)?**

DNS is like the **GPS of the internet** — it translates human-friendly domain names (like `www.example.com`) into machine-friendly IP addresses (like `192.0.2.1`) so your device knows where to send requests.

### **How DNS Works (Step-by-Step)**

1. **Your computer checks local cache**
    
    Already knows the IP? Connect directly.
    
2. **Asks DNS Resolver (ISP or public DNS like 8.8.8.8)**
    - If resolver has the answer → done!
    - If not, it performs a **recursive query**…
3. **Goes to Root Name Server**
    
    Doesn’t have the IP, but tells you where to look next — the **TLD name server**.
    
4. **TLD Name Server (.com, .org, etc.)**
    
    Points to the **Authoritative Name Server** of `example.com`.
    
5. **Authoritative Name Server**
    
    Holds the actual IP → sends it back.
    
6. **DNS Resolver caches & returns the IP**
    
    Future lookups are faster.
    
7. **Your Computer Connects**
    
    Boom! You’re on the website.
    

*This whole process takes milliseconds.*

### **The Hosts File**

A manual override before DNS — useful for:

- Development (e.g., `127.0.0.1 mysite.local`)
- Testing servers directly by IP
- Blocking sites (e.g., `0.0.0.0 badsite.com`)

Windows: `C:\Windows\System32\drivers\etc\hosts`

Linux/macOS: `/etc/hosts`

### **Think of DNS Like a Relay Race**

- Each “runner” (resolver → root → TLD → authoritative) passes the baton (query) to the next until the final IP address is found.

### **Key DNS Concepts & Record Types**

| Concept      | Description                                  | Example                                  |
|--------------|----------------------------------------------|------------------------------------------|
| A Record     | Maps domain to IPv4 address                  | `www.example.com IN A 192.0.2.1`         |
| AAAA Record  | Maps domain to IPv6 address                  | `www.example.com IN AAAA ...`            |
| CNAME        | Alias from one domain to another             | `blog IN CNAME www.example.com`          |
| MX Record    | Mail server                                  | `example.com IN MX 10 mail.example.com`  |
| NS Record    | Name servers for domain                      | `example.com IN NS ns1.example.com`      |
| TXT Record   | Text info, often for security like SPF, DKIM | `example.com IN TXT "v=spf1 mx -all"`    |
| SOA Record   | Start of Authority — admin info for the zone | Contains serial, TTLs, email             |
| PTR Record   | Reverse DNS lookup (IP → domain)             | `1.2.0.192.in-addr.arpa IN PTR www.example.com` |
| SRV Record   | Specifies service & port info                | `_sip._udp.example.com IN SRV ...`       |

### **Why DNS Matters for Penetration Testing / Web Recon**

**Uncover hidden assets**: Subdomains, outdated aliases via CNAMEs, or dev servers.

- **Map infrastructure**: Understand server setups, email systems, CDN usage, load balancers, etc.
- **Monitor changes**: New subdomains might hint at new services or entry points.
- **Security posture**: Misconfigured DNS records can lead to information leakage or exploitation.

If you're preparing for a **TryHackMe** room or a **pentesting project**, this DNS knowledge is pure gold. Let me know if you'd like:

- Visual diagrams for this
- Practice questions
- A cheat sheet for record types
- A real-world example of DNS-based recon