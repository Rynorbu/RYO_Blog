---
title: WHOIS
published: 2025-04-21
description: "A simplified guide to WHOIS in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering
draft: false
---

## WHOIS Protocol - Detailed Notes

### What is WHOIS?

WHOIS is a **query and response protocol** used to access databases containing information about **registered internet resources**. It's like a **giant phonebook** for the internet.

### What Can WHOIS Be Used For?

- **Domain Names**: Most commonly used to look up domain name registrations.
- **IP Address Blocks**: Provides ownership details.
- **Autonomous Systems**: Information about network management.


## Sample WHOIS Output

```jsx
K4y0x13@htb[/htb]$ whois inlanefreight
[...]
Domain Name: inlanefreight.com
Registry Domain ID: 2420436757_DOMAIN_
Registrar WHOIS Server: whois.registra
Registrar URL: https://registrar.amazo
Updated Date: 2023-07-03T01:11:15Z
Creation Date: 2019-08-05T22:43:09Z
[...]
```

### Common Fields in WHOIS Records

| Field | Description |
| --- | --- |

| **Domain Name** | The registered domain (e.g., `example.com`) |
| --- | --- |

| **Registrar** | The company where the domain was registered (e.g., GoDaddy, Namecheap) |
| --- | --- |

| **Registrant Contact** | The individual/organization that owns the domain |
| --- | --- |

| **Administrative Contact** | Person responsible for managing the domain |
| --- | --- |

| **Technical Contact** | Person responsible for technical issues related to the domain |
| --- | --- |

| **Creation Date** | When the domain was first registered |
| --- | --- |

| **Expiration Date** | When the domain is set to expire |
| --- | --- |

| **Name Servers** | Servers responsible for resolving the domain name into IP addresses |
| --- | --- |
|  |  |

### History of WHOIS

- **Origin**: Developed in the **1970s** by **Elizabeth Feinler** and her team at **Stanford Research Institute's Network Information Center (NIC)**.
- **Purpose**: To manage and track network resources on **ARPANET**, the precursor to the internet.
- **Innovation**: Created the **WHOIS directory**, a groundbreaking database that held user, hostname, and domain info.


### Why WHOIS Matters for Web Recon

WHOIS is a **powerful reconnaissance tool** for penetration testers. It provides insights into the target organization’s online infrastructure and potential vulnerabilities.

### Key Benefits

- **Identifying Key Personnel**
    - WHOIS records often list names, email addresses, and phone numbers of administrators.
    - Can be used in **social engineering** and **phishing attacks**.
- **Discovering Network Infrastructure**
    - Name servers, IP addresses, and registrars give clues about the target’s technical setup.
    - Helps identify **entry points**, **subdomains**, or **misconfigurations**.
- **Historical Data Analysis**
    - Services like [WhoisFreaks](https://whoisfreaks.com/) allow access to **past WHOIS records**.
    - Useful for spotting:
        - Ownership changes
        - Contact information changes
        - Server infrastructure evolution


### Summary

WHOIS remains a foundational tool in cybersecurity and digital forensics. Whether you're investigating a suspicious domain or conducting red team assessments, understanding and utilizing WHOIS data can give you a significant edge.