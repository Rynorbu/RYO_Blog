---
title: Well-Known URIs
published: 2025-04-21
description: "A simplified guide for Well-Known URIs in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Web Information Gathering 
draft: false
--- 

## `.well-known` URIs — Detailed Notes

### What is `.well-known`?

The `.well-known` standard, defined in **RFC 8615**, provides a **standardized path** (**/.well-known/**) within a website's root directory. This location is used to **host metadata and configuration files** related to the site's services, protocols, and security mechanisms.

> It ensures that clients, browsers, and tools can automatically discover important configurations by visiting known paths without relying on guesswork or crawling.
> 

### Purpose

- Centralizes **critical metadata**
- Enables **automatic discovery** of service-related configuration files
- Useful for **security tools**, **web browsers**, **apps**, and **penetration testers**

**Example:**

```jsx
https://example.com/.well-known/security.txt
```

This path leads to a **security contact file** for reporting vulnerabilities.


## Maintained by IANA

The **Internet Assigned Numbers Authority (IANA)** maintains a registry of **.well-known** URIs. Each URI serves a specific purpose, defined by official specifications.


## Notable **.well-known** URIs

| URI Suffix              | Description                                                                 | Status      | Reference               |
|-------------------------|-----------------------------------------------------------------------------|-------------|-------------------------|
| `security.txt`          | Contact info for security researchers to report vulnerabilities             | Permanent   | RFC 9116               |
| `change-password`       | Directs users to a password change page                                     | Provisional | W3C Spec               |
| `openid-configuration`  | Provides OpenID Connect metadata                                            | Permanent   | OpenID Discovery Spec  |
| `assetlinks.json`       | Verifies app-to-domain ownership (Android, etc.)                            | Permanent   | Google Spec            |
| `mta-sts.txt`          | Defines SMTP MTA-STS policy for email security                              | Permanent   | RFC 8461               |

## **.well-known** in Web Reconnaissance

During **web recon**, **.well-known** URIs can be goldmines for discovering hidden **endpoints**, **configurations**, and **security policies**.

### Example: **openid-configuration**

This URI is used in the **OpenID Connect Discovery Protocol**, built on top of OAuth 2.0. It allows client applications to automatically retrieve identity and authorization-related metadata.

### URL:

```java
https://example.com/.well-known/openid-configuration
```

**What it returns:**

```java
{
"issuer": "[https://example.com](https://example.com/)",
"authorization_endpoint": "https://example.com/oauth2/auth",
"token_endpoint": "https://example.com/oauth2/token",
"userinfo_endpoint": "https://example.com/oauth2/userinfo",
"jwks_uri": "https://example.com/oauth2/jwks",
"response_types_supported": ["code", "token", "id_token"],
"subject_types_supported": ["public"],
"id_token_signing_alg_values_supported": ["RS256"],
"scopes_supported": ["openid", "profile", "email"]
}
```

## Why This is Useful in Web Recon

### 1. **Endpoint Discovery**

- **authorization_endpoint**: Where login/auth requests are sent
- **token_endpoint**: Issues access tokens
- **userinfo_endpoint**: Returns profile info of authenticated users

### 2. **JWKS URI**

- **jwks_uri** points to public keys used to sign JWTs
- Useful for **validating tokens** and understanding key rotation strategies

### 3. **Scopes and Response Types**

- Shows which **user info and permissions** (scopes) are available
- Helps you identify the **extent of accessible data**

### 4. **Security Insight**

- **id_token_signing_alg_values_supported** reveals **crypto algorithms** used
- Helps assess whether **strong encryption** is in place

### Takeaway

Exploring **.well-known** URIs can help uncover:

- Internal configuration and metadata
- Application security policies
- Authorization/authentication infrastructure
- Sensitive or overlooked endpoints


### Final Tip

Visit the IANA **.well-known** URI registry:
👉 https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml

It provides an up-to-date list of registered and proposed `.well-known` paths and their usage.