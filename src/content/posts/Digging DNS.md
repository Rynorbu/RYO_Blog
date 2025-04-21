---
title: Digging DNS
published: 2025-04-21
description: "A guide to Digging DNS techniques and best practices."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

## Digging into DNS Reconnaissance

Now that we have a solid grasp of DNS fundamentals and record types, it’s time to explore the **practical tools and techniques** used in DNS reconnaissance — a crucial step in web recon.

### DNS Tools Overview

DNS reconnaissance involves leveraging tools to query DNS servers and extract valuable information. Below are some of the most popular tools used by web recon professionals:

| Tool                        | Key Features                                                                 | Use Cases                                                                 |
|-----------------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| dig                         | Versatile DNS lookup tool supporting various query types with detailed output | Manual DNS queries, zone transfers, troubleshooting, in-depth analysis   |
| nslookup                    | Simple tool for looking up A, AAAA, and MX records                           | Quick checks for domain resolution and mail servers                      |
| host                        | Streamlined tool with concise output                                         | Quick DNS checks (A, AAAA, MX records)                                   |
| dnsenum                     | Automated tool for DNS enumeration, dictionary attacks, and brute-forcing    | Discovering subdomains, gathering DNS data efficiently                   |
| fierce                      | Subdomain enumeration with recursive search and wildcard detection           | User-friendly subdomain discovery and DNS recon                          |
| dnsrecon                     | Combines various DNS techniques and supports different output formats        | Comprehensive DNS recon, subdomain identification, record collection     |
| theHarvester                | OSINT tool that also gathers DNS-related data                                | Gathering emails, employee info, and related data across sources         |
| Online DNS Lookup Services  | Web-based DNS lookup interfaces                                              | Convenient when CLI tools are unavailable; basic lookups and checks      |


### The Domain Information Groper (dig)

**dig** (Domain Information Groper) is a powerful and flexible command-line tool for querying DNS servers and retrieving DNS records.

### Common `dig` Commands

| Command                         | Description                                                                 |
|----------------------------------|-----------------------------------------------------------------------------|
| `dig domain.com`                | Default A record lookup for the domain                                     |
| `dig domain.com A`              | Retrieves IPv4 address (A record)                                          |
| `dig domain.com AAAA`           | Retrieves IPv6 address (AAAA record)                                       |
| `dig domain.com MX`             | Retrieves mail server records                                               |
| `dig domain.com NS`             | Retrieves name server records                                              |
| `dig domain.com TXT`            | Retrieves TXT records                                                      |
| `dig domain.com CNAME`          | Retrieves canonical name (CNAME) record                                    |
| `dig domain.com SOA`            | Retrieves Start of Authority (SOA) record                                  |
| `dig @1.1.1.1 domain.com`       | Queries a specific name server (e.g., Cloudflare)                          |
| `dig +trace domain.com`         | Displays full DNS resolution path                                          |
| `dig -x 192.168.1.1`           | Reverse lookup to get domain from IP                                       |
| `dig +short domain.com`         | Outputs only the final answer(s)                                           |
| `dig +noall +answer domain.com` | Shows only the answer section                                              |
| `dig domain.com ANY`            | Tries to retrieve all DNS records (limited due to abuse protection per RFC 8482) |

### Example `dig` Output Breakdown

```jsx
K4y0x13@htb[/htb]$ dig google.com
```

```jsx
; <<>> DiG 9.18.24-0ubuntu0.22.04.1-Ubuntu <<>> google.com
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16449
;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;google.com. IN A

;; ANSWER SECTION:
google.com. 0 IN A 142.251.47.142

;; Query time: 0 msec
;; SERVER: 172.23.176.1#53(172.23.176.1)
;; WHEN: Thu Jun 13 10:45:58 SAST 2024
;; MSG SIZE rcvd: 54
```

### Key Sections Explained

1. **Header**
    - **opcode: QUERY**, **status: NOERROR**: Indicates a successful query.
    - **id: 16449**: Unique ID for the query.
    - **flags: qr rd ad**:
        - **qr**: This is a response.
        - **rd**: Recursion was requested.
        - **ad**: Data is considered authentic.
2. **Question Section**
    - **google.com. IN A**: The query is asking for the A (IPv4) record of **google.com**.
3. **Answer Section**
    - **google.com. 0 IN A 142.251.47.142**: The IPv4 address is **142.251.47.142**.
        
        TTL is **0**, meaning it’s not cached.
        
4. **Footer**
    - **Query time**, **Server**, **When**, and **Message size** give context on timing, the server queried, and response size.

> Note: Some DNS servers do not support recursion or may limit the types of queries they respond to, especially ANY queries.
> 

### Quick Output with `+short`

```jsx
K4y0x13@htb[/htb]$ dig +short hackthebox.com
104.18.20.126  
104.18.21.126
```

This provides a quick, clean output with just the IP addresses — great for scripting or quick checks.


### Responsible DNS Recon

**Always remember:**

- Don't spam DNS queries.
- Respect rate limits and access permissions.
- Only perform DNS recon on domains you own or have permission to analyze.