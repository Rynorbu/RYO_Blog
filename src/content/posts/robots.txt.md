---
title: robots.txt
published: 2025-04-21
description: "A simplified guide to robots.txt in web development."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---

### Understanding **robots.txt**

Imagine you're at a grand house party. While you're free to explore, there are certain rooms marked "Private" that you're expected to avoid. Similarly, **robots.txt** is a file that tells web crawlers (bots) which parts of a website they can and cannot access. It's like an *etiquette guide for bots*.


### What is **robots.txt**?

- **robots.txt** is a simple **text file** placed in the **root directory** of a website:
    
    ```jsx
    https://www.example.com/robots.txt
    ```
    
- It follows the **Robots Exclusion Standard**, which provides **rules for web crawlers** on how to behave.
- The file contains **directives** that:
    - Tell bots **what to crawl**
    - Tell bots **what not to crawl**


## How **robots.txt** Works

### Example:

```jsx
User-agent: *
Disallow: /private/
```

- **User-agent:** → Applies to **all bots**
- **Disallow: /private/** → **Disallows access** to URLs starting with **/private/**

Other directives can:

- Allow access to specific files
- Introduce crawl delays
- Point bots to a sitemap for easier navigation


### **robots.txt** Structure

- It's a **plain text file**
- Each "record" contains:
    - One or more lines starting with **User-agent**
    - Followed by **directives**
- Each record is separated by a **blank line**


## Key Components

### 1. **User-agent**

- Specifies which **bot** the rules apply to
- Use  to target **all bots**
- Examples:
    - `Googlebot` → Google’s bot
    - `Bingbot` → Microsoft’s bot

### 2. **Directives**

| Directive | Description | Example |
| --- | --- | --- |

| **Disallow** | Blocks bot from accessing certain paths | **Disallow: /admin/** |
| --- | --- | --- |

| **Allow** | Lets bot access paths even if previously disallowed | **Allow: /public/** |
| --- | --- | --- |

| **Crawl-delay** | Adds a delay (in seconds) between requests to avoid server load | **Crawl-delay: 10** |
| --- | --- | --- |

| **Sitemap** | Provides link to sitemap for more efficient crawling | **Sitemap: https://example.com/sitemap.xml** |
| --- | --- | --- |

## Why Respect **robots.txt**?

Even though it's not enforced by default, most **legitimate bots** (like Googlebot) will respect it. Here's why it's important:

1. **Avoid Server Overload**
    - Limits bot traffic and prevents server crashes
2. **Protect Sensitive Information**
    - Keeps private or admin pages from being indexed
3. **Legal and Ethical Concerns**
    - Ignoring **robots.txt** may breach a site’s terms of service

### **robots.txt** in Web Reconnaissance

Security professionals often analyze **robots.txt** for **intel**, especially during **web reconnaissance**.

### What they look for:

- **Hidden Directories**
    - Paths in **Disallow** might expose:
        - Admin panels
        - Backup files
        - Sensitive resources
- **Site Structure Mapping**
    - By reading allowed/disallowed paths, one can guess the internal structure
- **Crawler Traps (Honeypots)**
    - Some sites set traps for bad bots
    - Helps identify the site's **defensive measures**


### Example **robots.txt** File

```jsx
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

User-agent: Googlebot
Crawl-delay: 10

Sitemap: https://www.example.com/sitemap.xml
```

### Interpretation:

- All bots:
    - Cannot access **/admin/** and **/private/**
    - Can access **/public/**
- Googlebot:
    - Must wait **10 seconds** between each request
- A **sitemap** is provided for better crawling and indexing

### Inference:

- Site may have an **admin panel** at **/admin/**
- Site has **private content** at **/private/**
- **/public/** is intentionally accessible to bots