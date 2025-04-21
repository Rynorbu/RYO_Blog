---
title: robots.txt
published: 2025-04-21
description: "A simplified guide to robots.txt in web development."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
---


## Search Engine Discovery (OSINT)

Search Engine Discovery is the practice of using public search engines to gather information about websites, organizations, or individuals. It's a form of **Open Source Intelligence (OSINT)** that leverages advanced search techniques to extract valuable data from publicly accessible sources.

### Why It Matters

- **Open Source**: Legal and ethical since it uses public data.
- **Extensive Reach**: Accesses a wide range of indexed online content.
- **User-Friendly**: No deep technical expertise required.
- **Free Tool**: Cost-effective for researchers, analysts, and security professionals.

### Applications

- **Security Assessment**: Detect exposed endpoints, sensitive documents, and credentials.
- **Competitive Intelligence**: Analyze competitors' offerings and strategies.
- **Investigative Journalism**: Trace hidden connections and transactions.
- **Threat Intelligence**: Identify and monitor emerging cyber threats.

### Search Operators Cheat Sheet

| Operator      | Description                          | Example                          | Use Case                                  |
|--------------|--------------------------------------|----------------------------------|-------------------------------------------|
| `site:`      | Restrict search to a specific domain | site:example.com               | View all indexed pages on a site          |
| `inurl:`     | Search term within URL               | inurl:login                    | Find login or admin pages                 |
| `filetype:`  | Search for a specific file type      | filetype:pdf                   | Locate PDFs, docs, etc.                   |
| `intitle:`   | Term in page title                   | intitle:"confidential report  | Discover sensitive or titled content      |
| `intext:`    | Term in body text                    | intext:"reset password"        | Find mentions in site content             |
| `cache:`     | View cached version                  | cache:example.com              | See previous content of a site            |
| `link:`      | Find sites linking to a URL          | link:example.com               | Analyze backlinks                         |
| `related:`   | Show similar sites                   | related:example.com          | Discover competitors or alternative domains |
| `info:`      | Show page summary                    | info:example.com               | Basic metadata and indexing               |
| `define:`    | Get definitions                      | define:phishing               | Clarify terms quickly                     |
| `numrange:`  | Filter within a number range         | numrange:1000-2000             | Find pages mentioning numbers in a range  |
| `allintext:` | All terms must be in body            | allintext:admin reset password | Match multiple body terms                 |
| `allinurl:`  | All terms must be in URL             | allinurl:admin panel           | Search for specific structures            |
| `allintitle:`| All terms must be in title           | allintitle:confidential report 2023 | Find precisely titled pages         |
| `OR`         | Either of the terms                  | "ubuntu" OR "debian"           | Broaden your search scope                 |
| `AND`        | Both terms required                  | site:example.com AND inurl:login | Narrow to more specific queries          |
| `NOT` or `-` | Exclude terms                        | site:bank.com -inurl:login     | Remove unwanted results                   |
| `*`          | Wildcard                             | filetype:pdf user* guide       | Match anything between or after words     |
| `..`         | Numerical range                      | "price" 100..500               | Find products within price range          |
| `" "`        | Exact phrase                         | "security best practices"      | Avoid variations, get precise matches     |

### Google Dorking (aka Google Hacking)

**Google Dorking** uses the above search operators to uncover sensitive or hidden data from websites. While often used by security researchers and ethical hackers, it must be handled responsibly.

### Common Google Dork Examples

| Purpose               | Dork Example                                                                 |
|-----------------------|------------------------------------------------------------------------------|
| Find Login Pages      | site:example.com inurl:login OR inurl:admin                               |
| Exposed Files         | site:example.com filetype:pdf OR filetype:xls OR filetype:docx            |
| Config Files          | inurl:config.php OR ext:conf OR ext:cnf                                   |
| Database Backups      | inurl:backup OR filetype:sql                                              |
| Sensitive Info Leaks  | intitle:index.of passwd OR intext:password                                |


For a full collection, refer to the Google Hacking Database (GHDB).

### Limitations

- Search engines **don’t index** everything (deep web, dynamic content).
- Some data is **intentionally hidden** or protected by firewalls/robots.txt.
- Always ensure you're operating within **legal and ethical boundaries**.