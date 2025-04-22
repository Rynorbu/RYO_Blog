---
title: SMTP
published: 2025-04-20
description: "A simplified guide to SMTP in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Footprinting
draft: false
---

### **SMTP Overview**

- **SMTP (Simple Mail Transfer Protocol)** is used to **send** emails over IP networks.
- Works **client-server** or **server-to-server**.
- Default port is **25**, but:
    - **Port 587** is used for mail submission with STARTTLS.
    - **Port 465** is used for SMTPS (SMTP over SSL).


### **Encryption & Security**

- **Unencrypted by default** — sends data, including authentication credentials, in **plaintext**.
- Use **STARTTLS** or **SMTPS (port 465)** to **encrypt** the connection.
- **SMTP-Auth (ESMTP)** allows client authentication and helps prevent **open relay** misuse.

### **Security Mechanisms**

- **DKIM (DomainKeys Identified Mail)** – Email validation using cryptographic signatures.
- **SPF (Sender Policy Framework)** – Prevents sender address spoofing.
- **MTA (Mail Transfer Agent)** – Main component that routes messages.
- **MSA (Mail Submission Agent)** – Pre-processes email and checks origin.
- **MDA (Mail Delivery Agent)** – Delivers to the correct recipient’s mailbox (POP3/IMAP).

### **Penetration Testing with SMTP**

### Enumeration using Telnet

- You can **interact directly** with SMTP via Telnet:
    
    ```jsx
    telnet <IP> 25
    HELO <domain>
    EHLO <domain>
    ```
    
- **User enumeration** using `VRFY`:

    ```jsx
    VRFY root
    252 2.0.0 root
    ```

- Note: **Code 252** does not guarantee existence; could be a catch-all response.

### Send Email via Telnet

Example sequence:

```jsx
EHLO domain.com
MAIL FROM: <sender@example.com>
RCPT TO: <recipient@example.com>
DATA
From: ...
To: ...
Subject: ...
[message body]
.
QUIT
```

### **Default SMTP Configuration (Postfix)**

Example config values:

- `myhostname = mail1.inlanefreight.htb`
- `inet_protocols = ipv4`
- `mynetworks = 127.0.0.0/8 10.129.0.0/16` — Trusts only specific subnets.
- `smtpd_helo_restrictions = reject_invalid_hostname` — Basic anti-spam.

### **SMTP Weaknesses**

1. **No guaranteed delivery confirmation**.
2. **Lack of sender authentication** can lead to:
    - **Mail spoofing**
    - **Open relay abuse** (spammers using your server)


### Email Header

- Contains **metadata**: sender, recipient, routing info, timestamps.
- **Visible to both sender and recipient** (via email client UI or raw view).
- Useful in **forensics** and **tracking spoofed or malicious emails**.

### Summary of SMTP Commands

| Command       | Description                          | Security Notes                  |
|---------------|--------------------------------------|----------------------------------|
| **EHLO/HELO** | Initiate SMTP session                | Modern servers prefer EHLO       |
| **MAIL FROM** | Specify sender address               | Often spoofed in spam/phishing  |
| **RCPT TO**   | Specify recipient address            | Can enumerate valid users       |
| **DATA**      | Begin email content transmission     | Ends with CRLF.CRLF             |
| **QUIT**      | Gracefully terminate connection      |                                  |
| **VRFY**      | Verify if user exists (disabled)      | ❗ Common attack vector          |
| **EXPN**      | Expand mailing list (disabled)       | ❗ Information disclosure risk   |
| **NOOP**      | Keep connection alive                | Used to bypass timeouts         |
| **RSET**      | Abort current transaction            | Doesn't close connection        |
| **STARTTLS**  | Upgrade to encrypted connection      | Essential for security          |

### Pro Tips

- Always check if **VRFY** is enabled—it’s useful for **user enumeration**.
- Use **`telnet`, `netcat`, or `openssl s_client`** to manually test SMTP communication.
- Watch for **open relays** (misconfigured servers that allow unauthenticated sending).
- Use **headers** for recon—internal IPs, software, time stamps.