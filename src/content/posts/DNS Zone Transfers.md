---
title: DNS Zone Transfers
published: 2025-04-21
description: "A guide to DNS Zone Transfers techniques and best practices."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

### **What is a DNS Zone Transfer (AXFR)?**

A **zone transfer** is a mechanism where one DNS server copies all DNS records from another. This usually happens between a **primary (master)** and **secondary (slave)** DNS server to ensure redundancy and consistency.

### The Steps of a Zone Transfer:

1. **AXFR Request**: The secondary server sends a zone transfer request to the primary.
2. **SOA Record**: The primary server responds with the Start of Authority record.
3. **DNS Record Transmission**: The server sends all records (A, MX, CNAME, etc.).
4. **Completion Signal**: The primary server signals the end of transfer.
5. **ACK**: The secondary server acknowledges receipt.

### **Why Is It a Security Risk?**

If a DNS server is **misconfigured to allow AXFR requests from anyone**, an attacker can:

- **Harvest all subdomains**, some of which may expose internal systems like:
    - dev.example.com
    - staging.example.com
    - admin.example.com
- Get **IP addresses** linked to these subdomains.
- See **mail servers (MX records)** and possibly internal infrastructures.
- Identify **name servers**, hinting at third-party services or misconfigurations.

### **How to Prevent Zone Transfer Vulnerabilities:**

- Only allow AXFR to **trusted IPs (secondary DNS servers)**.
- Use **TSIG (Transaction Signatures)** to secure DNS communication.
- Regularly **audit DNS server configurations**.

### **Testing with dig:**

To test if a server allows AXFR:

```jsx
dig axfr @<dns-server> <domain>
```

Example:

```jsx
dig axfr @nsztm1.digi.ninja zonetransfer.me
```

If the server is vulnerable, you’ll get a full dump of the zone records like this:

```jsx
asfdbbox.zonetransfer.me. 7200 IN A 127.0.0.1
canberra-office.zonetransfer.me. 7200 IN A 202.14.81.230
...
```

**zonetransfer.me** is a legal playground domain for testing this vulnerability.

### **Extra Tip (for CTFs or Pentesting):**

If zone transfer is blocked, try other recon methods:

- **Subdomain brute forcing** using tools like **sublist3r**, **amass**, **assetfinder**
- Look at **SSL certificates** (via **crt.sh**)
- Analyze **JavaScript files** on the site for hidden endpoints
- Try **Google Dorking**: **site:example.com -www**