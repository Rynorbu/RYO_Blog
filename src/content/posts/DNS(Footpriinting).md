---
title: DNS
published: 2025-04-20
description: "A simplified guide to DNS in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Footprinting
draft: false
---

### **What is DNS (Domain Name System)?**

DNS translates **domain names** (like `www.hackthebox.com`) into **IP addresses** (like `104.22.3.84`). Think of it like a **phone book** for the internet.

It doesn't use a central database — DNS info is distributed globally across thousands of **name servers**.

### **Types of DNS Servers**

| Server Type               | Description |
|---------------------------|-------------|
| **DNS Root Server**        | Top-level servers managed by ICANN that resolve TLDs (.com, .net). Only queried when other servers can't answer. |
| **Authoritative Nameserver** | The official source for domain records. Provides definitive answers for its zones. |
| **Non-authoritative Nameserver** | Answers queries using cached data obtained from other servers, not from zone files. |
| **Caching DNS Server**     | Temporarily stores DNS records to improve performance. Respects TTL values from authoritative servers. |
| **Forwarding Server**      | Passes DNS queries to designated resolvers instead of resolving them directly. |
| **Resolver**              | Client-side component (in devices/routers) that initiates DNS lookup requests. |

### **Security & Privacy**

DNS traffic is **unencrypted by default**, making it susceptible to spying. To improve privacy:

- **DNS over TLS (DoT)**
- **DNS over HTTPS (DoH)**
- **DNSCrypt**

### **Common DNS Records**

| Record | Purpose | Example Use Case |
|--------|---------|------------------|
| **A** | Maps domain to IPv4 address | `example.com → 192.0.2.1` |
| **AAAA** | Maps domain to IPv6 address | `example.com → 2001:db8::1` |
| **MX** | Specifies mail servers | `10 mail.example.com` |
| **NS** | Delegates to authoritative nameservers | `ns1.example.com` |
| **TXT** | Contains text data (verification, email security) | `"v=spf1 include:_spf.google.com ~all"` |
| **CNAME** | Creates domain aliases | `www → example.com` |
| **PTR** | Reverse DNS (IP to name) | `1.2.0.192.in-addr.arpa → example.com` |
| **SOA** | Zone administration details | `Primary nameserver, admin email, serial number` |

### **Example: Using `dig` to find SOA**

```jsx
dig soa www.inlanefreight.com
```

**Result:**

- SOA Record found for `inlanefreight.com`
- Email: `awsdns-hostmaster@amazon.com` (dot replaced with `@`)
- Server: `ns-161.awsdns-20.com`

### **DNS Server Configuration (BIND9)**

### 1. **Local Config Files**

- `/etc/bind/named.conf.local`
- `/etc/bind/named.conf.options`
- `/etc/bind/named.conf.log`

### 2. **Zone Files**

Located at: `/etc/bind/db.domain.com`

Example entry:

```jsx
@ IN SOA dns1.domain.com. hostmaster.domain.com. (
2001062501 ; serial
21600 ; refresh
3600 ; retry
604800 ; expire
86400 ) ; minimum TTL
IN NS ns1.domain.com.
IN A 10.129.14.5
www IN CNAME server2
```

### **Reverse Lookup Files**

Located at: `/etc/bind/db.10.129.14`

Example PTR:

```jsx
5 IN PTR server1.domain.com.
```

### **Dangerous Settings**

Misconfigurations can expose the DNS server to attacks. Some common issues:

- Too open `allow-query` settings
- Allowing zone transfers publicly
- Running outdated BIND versions (vulnerabilities listed at CVEdetails)
- Prioritizing functionality over security