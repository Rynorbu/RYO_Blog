---
title: SNMP
published: 2025-04-20
description: "A simplified guide to SNMP in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Footprinting
draft: false
---

## Simple Network Management Protocol (SNMP) in Penetration Testing

### Introduction to SNMP
Simple Network Management Protocol (SNMP) is a protocol designed for monitoring and managing network devices, including:
- Routers
- Switches
- Servers
- IoT devices
- Various network-enabled devices

**Key Capabilities:**
- Remote monitoring
- Configuration management
- Event notification
- Uses UDP ports 161 (queries) and 162 (traps)

### SNMP Versions and Security

| Version | Security Features | Vulnerabilities |
|---------|-------------------|-----------------|
| **SNMPv1** | - No authentication<br>- No encryption | - Plaintext community strings<br>- Susceptible to spoofing |
| **SNMPv2c** | - Community-based authentication | - Still uses plaintext<br>- Limited security improvements |
| **SNMPv3** | - Username/password auth<br>- Encryption support<br>- Message integrity | - Complex configuration<br>- Not universally adopted |

## SNMP Architecture Components

### 1. Management Information Base (MIB)
- Hierarchical database of managed objects
- Standardized format across vendors
- Written in ASN.1 format
- Defines structure of available information

### 2. Object Identifiers (OIDs)
- Unique identifiers for each managed object
- Dot notation (e.g., .1.3.6.1.2.1.1.1.0)
- Longer path = more specific information
- Organized in a global tree structure

### SNMP Configuration Analysis

### Sample snmpd.conf
```bash
cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'
```

**Common Directives:**

- **rocommunity**: Read-only community string
- **rwcommunity**: Read-write community string
- **sysLocation**: Device location info
- **sysContact**: Administrator contact

### Dangerous Configurations

| Setting | Risk |
| --- | --- |
| **rwuser noauth** | Full OID tree access without authentication |
| **rwcommunity <string>** | Read-write access from any IP |
| Default community strings | Easy guessing (public/private) |

### SNMP Enumeration Techniques

### 1. SNMP Walk

```bash
snmpwalk -v2c -c public <target_IP>
```

**Information Typically Revealed:**

- System details (OS, version)
- Network interfaces
- Running processes
- Installed software
- User accounts

### 2. Community String Brute-forcing

```bash
onesixtyone -c /path/to/wordlist.txt <target_IP>
```

**Common Wordlists:**

- SecLists/Discovery/SNMP/
- Custom lists based on organization naming

### 3. Bulk OID Enumeration with Braa

```bash
braa public@<target_IP>:.1.3.6.*
```

**Advantages:**

- Fast parallel queries
- Useful for identifying interesting OIDs

## SNMP Attack Vectors

### 1. Information Disclosure

- Network topology mapping
- System configuration details
- User account enumeration

### 2. Configuration Modification

- Changing network settings
- Modifying system parameters
- Creating backdoor accounts (if RW access)

### 3. Privilege Escalation

- Extracting credentials
- Accessing sensitive configuration files
- Modifying system binaries

## SNMP Security Best Practices

### For Administrators:

1. **Upgrade to SNMPv3** for authentication and encryption
2. **Use complex community strings** (avoid public/private)
3. **Implement ACLs** to restrict access
4. **Disable SNMP** if not required
5. **Regularly audit** snmpd.conf configurations

### For Penetration Testers:

1. **Verify permissions** before modifying values
2. **Document findings** thoroughly
3. **Check for sensitive data** in MIBs
4. **Look for RW access** opportunities
5. **Correlate findings** with other services

## Practical Examples

### Enumerating System Information

```bash
snmpwalk -v2c -c public 10.129.14.128 system
```

### Identifying Installed Packages

```bash
snmpwalk -v2c -c public 10.129.14.128 .1.3.6.1.2.1.25.6.3.1.2
```

### Checking for Write Access

```bash
snmpset -v2c -c private 10.129.14.128 <OID> <value>
```

### Tools for SNMP Testing

| Tool | Purpose |
| --- | --- |
| snmpwalk | Full OID tree enumeration |
| onesixtyone | Community string brute-forcing |
| braa | Fast bulk OID queries |
| snmp-check | Comprehensive SNMP auditing |
| Metasploit | SNMP enumeration modules |

## Conclusion

SNMP remains a valuable protocol for network management but poses  significant security risks when misconfigured. Penetration testers  should thoroughly examine SNMP services during assessments, as they  often provide a wealth of information and potential attack vectors. The transition from SNMPv1/v2c to v3 is strongly recommended for organizations, though many still rely on the insecure older versions due to compatibility concerns.