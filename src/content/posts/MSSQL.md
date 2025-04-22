---
title: MSSQL
published: 2025-04-20
description: "A simplified guide to MSSQL in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Footprinting
draft: false
---

### Key Points:

1. **MSSQL Overview**:
    - MSSQL is a closed-source, relational database management system developed by Microsoft. It is commonly used for applications that run on Microsoft's .NET framework.
    - It primarily runs on Windows but there are versions available for Linux and macOS.
2. **MSSQL Clients**:
    - **SQL Server Management Studio (SSMS)**: A key tool for managing databases. It can be installed separately or along with MSSQL.
    - Other clients include **mssql-cli**, **SQL Server PowerShell**, **HeidiSQL**, and **Impacket's mssqlclient.py**.
    - Impacket's **mssqlclient.py** is particularly useful for penetration testers due to its widespread presence in pentesting distributions.
3. **MSSQL Databases**:
    - Default system databases help understand the database structure:
        - `master`: Tracks all system information.
        - `model`: Template for creating new databases.
        - `msdb`: Used for scheduling jobs and alerts.
        - `tempdb`: Stores temporary objects.
        - `resource`: Read-only system objects.
4. **Default Configuration**:
    - **NT SERVICE\MSSQLSERVER** is the default account for the SQL service.
    - Windows Authentication is typically used by default, meaning the underlying OS handles the login requests.
5. **Dangerous Settings**:
    - **No encryption**: MSSQL clients may connect without encryption.
    - **Self-signed certificates**: These can be spoofed, leading to vulnerabilities.
    - **Named pipes**: Can be a potential attack vector.
    - **Weak or default credentials**: The "sa" (System Administrator) account is often left unsecured.
6. **Footprinting MSSQL**:
    - **NMAP**: Using Nmap scripts like `ms-sql-info`, `ms-sql-empty-password`, and others, can help gather information about the MSSQL instance, including version, named pipes, and configuration settings.
    - **Metasploit**: Using the `scanner/mssql/mssql_ping` auxiliary module in Metasploit can also gather similar information.
7. **Connecting to MSSQL**:
    - Once credentials are obtained, **Impacket's mssqlclient.py** can be used to connect to the MSSQL server and execute T-SQL commands.

### Example Scans:

- **NMAP MSSQL Script Scan**:
The scan reveals important details like the SQL Server version, instance name (`MSSQLSERVER`), and whether named pipes are enabled.
- **Metasploit MSSQL Ping**:
This module also provides key details like the version, server name, and whether the server is clustered.
- **Using mssqlclient.py**:
Once connected, a penetration tester can interact with the MSSQL server, retrieve information about databases, and execute commands like `SELECT name FROM sys.databases` to list the available databases.

### Potential Attack Vectors:

- Misconfigurations or insecure settings in MSSQL databases can lead to vulnerabilities. Penetration testers should focus on weak credentials, unencrypted connections, and unsafe configurations such as the use of named pipes or self-signed certificates.