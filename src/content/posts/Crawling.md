---
title: Crawling
published: 2025-04-21
description: "A guide to web crawling techniques and best practices."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

### Certificate Transparency (CT) Logs – The Watchdogs of Web Trust

**Core Idea:**

Certificate Transparency Logs are **public records** of all SSL/TLS certificates issued by Certificate Authorities (CAs). Their goal is to make certificate issuance transparent and **detect rogue or mis-issued certificates** early.

**Why it matters in security recon:**

- Helps detect **unauthorized subdomains** and **rogue certificates**.
- Exposes **old or expired certs**, which may lead to **vulnerable or forgotten infrastructure**.
- Acts as a **source for subdomain enumeration**—no brute-forcing required.

**Tools:**

- **crt.sh** – easy to use, good for quick checks.
- **Censys** – powerful for deep analysis and linking certs to hosts.

**Automation Example (with `curl` & `jq`):**

```jsx
curl -s "https://crt.sh/?q=facebook.com&output=json" \
| jq -r '.[] | select(.name_value | contains("dev")) | .name_value' \
| sort -u
```

### Crawling – Mapping the Web, One Link at a Time

**Core Idea:**

Crawling is like casting a web across the internet. Crawlers visit a page, extract links, then visit those links, and repeat the process.

**Two main crawling strategies:**

- **Breadth-First Crawling (BFS)** – good for full coverage.
- **Depth-First Crawling (DFS)** – useful for digging deep quickly.

**Valuable Data Extracted:**

- **Links** – find hidden or internal pages.
- **Comments** – users might leak useful info.
- **Metadata** – titles, descriptions, versions.
- **Sensitive Files** – backups, config files, logs.

**Context is Key:**

A single comment or exposed file might seem unimportant—but when cross-referenced with **other data points** (e.g. an accessible `/files/` directory or metadata showing outdated versions), it can **reveal real vulnerabilities**.

> Think like a detective: each piece of data is a clue, and together, they form a story.
> 

### TL;DR – Recon Power Combo

- Use **CT Logs** to uncover **subdomains**, **certificate abuse**, and **legacy assets**.
- Use **Crawlers** to extract **site architecture**, **sensitive files**, and **insights from context**.