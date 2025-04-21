---
title: Utilising WHOIS
published: 2025-04-21
description: "A simplified guide to Utilising WHOIS in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

### What is WHOIS?

**WHOIS** is a protocol used to query databases that store registered users or assignees of domain names, IP addresses, and autonomous systems. It can help uncover domain ownership, registration dates, contact info, and more.


### Why is WHOIS Important in Cybersecurity?

WHOIS can:

- Help identify malicious domain registrations
- Reveal ownership and registration details
- Detect patterns in cyber attacks
- Provide clues during phishing and malware investigations

### Real-World Scenarios

### Scenario 1: **Phishing Investigation**

**Situation:**

- A suspicious email appears to be from the company’s bank, urging employees to update their account info via a link.

**Action:**

- Security analyst performs a **WHOIS lookup** on the linked domain.

**WHOIS Findings:**

- **Registration Date:** Very recent (just a few days ago).
- **Registrant Info:** Hidden behind a privacy service.
- **Name Servers:** Associated with **bulletproof hosting** (often used for malicious activity).

**Conclusion:**

- The combination of these red flags suggests a phishing attack.
- The domain is blocked and employees are warned.
- Analyst continues to investigate the hosting provider and linked infrastructure for related threats.

### Scenario 2: **Malware Analysis**

**Situation:**

- Researcher analyzes malware that connects to a remote **Command and Control (C2)** server.

**Action:**

- WHOIS lookup is done on the C2 domain.

**WHOIS Findings:**

- **Registrant Email:** Uses a free and anonymous service.
- **Location:** Registered in a cybercrime-prone country.
- **Registrar:** Known for ignoring abuse complaints.

**Conclusion:**

- Domain likely hosted on a **compromised or bulletproof server**.
- Researcher contacts hosting provider to report abuse.

### Scenario 3: **Threat Intelligence Report**

**Situation:**

- Analysts track a **sophisticated threat actor group** targeting financial institutions.

**Action:**

- WHOIS data is collected across multiple domains.

**Patterns Discovered:**

- **Registration Dates:** Group registers domains in clusters before launching attacks.
- **Aliases:** Fake identities are used for registration.
- **Shared Infrastructure:** Same name servers used across multiple domains.
- **Takedown History:** Domains are taken down post-attack.

**Outcome:**

- Analysts document the group’s **Tactics, Techniques, and Procedures (TTPs)**.
- Report includes **Indicators of Compromise (IOCs)** for future detection.


## How to Use WHOIS

### Step 1: Install WHOIS

### Step 1: Install WHOIS

```jsx
sudo apt update
sudo apt install whois -y
```

### Step 2: Perform a WHOIS Lookup

Example using **facebook.com**:

```jsx
whois facebook.com
```

## Understanding WHOIS Output: facebook.com Example

### 1. Domain Registration

- **Registrar:** RegistrarSafe, LLC
- **Creation Date:** 1997-03-29
- **Expiry Date:** 2033-03-30

Suggests legitimacy and a long-established domain.


### 2.Domain Owner

- **Organization:** Meta Platforms, Inc.
- **Contact:** Domain Admin

Confirms ownership by a known and trusted company.

### 3. Domain Status

- **clientDeleteProhibited**
- **clientTransferProhibited**
- **clientUpdateProhibited**
- **serverDeleteProhibited**
- **serverTransferProhibited**
- **serverUpdateProhibited**

These statuses **prevent unauthorized changes** and protect the domain.

---

### 4. Name Servers

- **A.NS.FACEBOOK.COM**
- **B.NS.FACEBOOK.COM**
- **C.NS.FACEBOOK.COM**
- **D.NS.FACEBOOK.COM**

All name servers are internally managed by Facebook, which ensures **DNS security and reliability**.


### Final Notes

- WHOIS is a **valuable reconnaissance tool** in cybersecurity.
- It helps validate legitimacy and detect suspicious activity.
- However, WHOIS should be **used in combination** with other tools and techniques for thorough investigations.