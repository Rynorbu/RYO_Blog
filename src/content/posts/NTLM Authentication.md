---
title: "NTLM Authentication"
published: 2025-04-20
description: "A simplified guide to NTLM Authentication."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Acive Directory
draft: false
---

# NTLM Authentication

**Hash Types vs Protocols**

| **Hash Type** | **Used In** | **Hashing Algorithm** | **Security** | **Notes** |
| --- | --- | --- | --- | --- |
| **LM** | NTLM, NTLMv1 (historical) | Two DES chunks of uppercase password | Very weak | Easy to crack, limited charset, split into 7-char chunks |
| **NT Hash** (aka NTLM hash) | NTLM, NTLMv1, NTLMv2 | MD4(UTF-16-LE(password)) | Weak to brute-force | Still widely used, used in pass-the-hash attacks |
| **MSCache2** | Offline Login | MD4 + PBKDF2 (10240 iterations) | Better | Used for cached domain credentials |

**Authentication Protocol Comparison**

| **Protocol** | **Mutual Auth?** | **Hash Used** | **Security Level** | **Vulnerabilities** |
| --- | --- | --- | --- | --- |
| **NTLM** | No | LM / NT Hash | Weak | Pass-the-hash, relay attacks |
| **NTLMv1** | No | LM + NT | Very weak | Easily cracked, no server integrity |
| **NTLMv2** | No | NT Hash | Better than v1 | Still lacks mutual auth, vulnerable to relay |
| **Kerberos** | Yes | Ticket-based | Strongest | More secure, but complex setup |

### **Important Concepts**

- **NTLM (Basic)**: Uses a 3-message challenge-response protocol. Still used in fallback scenarios. Susceptible to *pass-the-hash*.
- **NTLMv1**: Challenge-response using DES encryption and the NT/LM hash. Weak, deprecated.
- **NTLMv2**: Uses HMAC-MD5 and client/server challenges. Stronger than v1, but still lacks true mutual authentication.
- **Kerberos**: Preferred in domain environments. Uses tickets and supports mutual auth, time-based access, and encryption.

---

### **Security Tools & Attacks**

- **Hashcat**: Used to brute-force or dictionary attack LM and NT hashes offline.
- **CrackMapExec**: Used for *pass-the-hash* attacks (especially NTLM).
- **Responder / NTLMRelayX**: Tools for capturing and relaying NTLM credentials on networks.

---

### **Key Security Takeaways**

- **Disable LM hashes** via Group Policy — they're too weak.
- **Avoid NTLMv1 entirely** — very outdated and insecure.
- **Restrict NTLMv2 usage** and enforce Kerberos wherever possible.
- **Monitor for pass-the-hash attacks** — NTLM hashes are reusable without cracking.
- **Use strong, long passwords** — helps prevent hash cracking.