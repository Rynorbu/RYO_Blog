---
title: FTP
published: 2025-04-20
description: "A simplified guide to FTP in cybersecurity."
# image: "../../assets/images/my_photo.jpg"
tags: ["Cyber"]
category: Footprinting
draft: false
---

### **FTP (File Transfer Protocol)**

**Purpose:**

FTP is used to upload and download files between a client and server over a network.

**Layer:**

- Application Layer (same as HTTP, POP3)

**Ports:**

- **Control Channel:** TCP **port 21** – used for commands/status codes
- **Data Channel:** TCP **port 20** – used for transferring files

**Active vs Passive Mode**

| Mode    | Description |
|---------|-------------|
| **Active** | Client connects to server's port 21 (control), then tells server which client port to connect back to for data transfer. *Fails with client-side firewalls.* |
| **Passive** | Server opens a random high port for data transfer and tells client where to connect. *Firewall-friendly (works with NAT).* |


### **FTP Commands Overview**

- `ls` — list directory contents
- `get <file>` — download file
- `put <file>` — upload file
- `status` — show connection status
- `debug` — enable detailed output
- `trace` — trace packets


### **Authentication & Security**

- **Default:** Requires username and password
- **Anonymous Access:** No credentials required (limited access)
- **Warning:** FTP transmits data in **clear text**, vulnerable to sniffing
- **Log Files & Configuration:**
    - `/etc/vsftpd.conf` – main config file
    - `/etc/ftpusers` – users denied access



### **Common `vsftpd` Configuration**

```jsx
# Show only active settings
cat /etc/vsftpd.conf | grep -v "#"
```

| Setting                      | Description                                                                 | Security Impact                     |
|------------------------------|-----------------------------------------------------------------------------|-------------------------------------|
| `anonymous_enable=NO`        | Disables anonymous FTP access                                               | Improves security                |
| `local_enable=YES`           | Allows system users to login                                                | Requires strong passwords        |
| `dirmessage_enable=YES`      | Shows `.message` files when entering directories                            | Potential info disclosure       |
| `write_enable=YES`           | Enables file modifications (upload/delete/rename)                           | Needs chroot/jail consideration  |
| `connect_from_port_20=YES`   | Uses port 20 for active mode data connections                               | Required for active mode         |
| `ssl_enable=NO`              | Disables SSL encryption (not recommended)                                    | Security risk (plaintext auth)   |
| `pam_service_name=vsftpd`    | Specifies PAM authentication configuration file                             | Tied to system auth              |
| `rsa_cert_file`/`rsa_private_key_file` | Paths to SSL certificate and private key files (when SSL enabled) | Essential for FTPS security      |

### **Blocked Users via `/etc/ftpusers`**

```jsx
cat /etc/ftpusers
```

### Example output:

```jsx
guest
john
kevin
```

These users are **blocked** from using FTP.


### **Dangerous (but sometimes used) Anonymous Settings**

```jsx
anonymous_enable=YES
anon_upload_enable=YES
anon_mkdir_write_enable=YES
no_anon_password=YES
anon_root=/home/username/ftp
write_enable=YES
```

These can **expose** your system if enabled without care. Use **only in trusted internal networks**.


### **Anonymous Login Example**

```jsx
ftp 10.129.14.136
Name: anonymous
```

```jsx
ftp> ls
ftp> get <filename>
ftp> status
ftp> debug
ftp> trace

```

### **TFTP (Trivial File Transfer Protocol)**

**Purpose:**

Used for simple file transfers, often in embedded or network boot environments.

**Comparison with FTP:**

| Feature            | FTP (File Transfer Protocol)              | TFTP (Trivial File Transfer Protocol) |
|--------------------|-------------------------------------------|---------------------------------------|
| **Transport**      | TCP (Port 21 control, 20 data)           | UDP (Port 69)                         |
| **Authentication** | Username/password required               | No authentication                     |
| **Encryption**     | None (cleartext)                         | None (cleartext)                      |
| **Security**       | Unencrypted                           | Highly insecure                  |
| **Command Set**    | Full (ls, put, get, etc.)                | Minimal (read/write only)             |
| **Directory Ops**  | Yes (list, navigate)                     | No                                    |
| **File Size**      | No practical limit                       | Limited (max 32MB per file)           |
| **Error Recovery** | Yes (reliable)                           | Basic (no retransmission guarantee)   |
| **Use Cases**      | Internet file transfers                  | LAN booting (PXE), config transfers   |
| **Speed**          | Moderate (TCP overhead)                  | Fast (UDP minimal overhead)           |

**TFTP Commands**

| Command  | Description           |
|----------|-----------------------|
| connect  | Set remote host/port  |
| get      | Download file         |
| put      | Upload file           |
| quit     | Exit                  |
| status   | Show status           |
| verbose  | Toggle verbosity      |

### **Useful Tips**

- `vsftpd` is commonly used on Linux systems and can be installed via:

```jsx
sudo apt install vsftpd
```

- For more configuration options, refer to the man page:

```jsx
man vsftpd.conf
```

### **Footprinting the Service with Nmap**

### Nmap and NSE (Nmap Scripting Engine)

- **Nmap** is a powerful scanner for discovering hosts, services, and vulnerabilities.
- It includes **NSE scripts** for specific services like FTP, HTTP, SMB, etc.
- To keep the script database up to date:
    
    ```jsx
    sudo nmap --script-updatedb
    ```
    
- Location of scripts (example):

    ```jsx
    find / -type f -name ftp* 2>/dev/null | grep scripts
    ```

### **FTP Enumeration**

### Scanning Target

Basic Nmap command for FTP service on port 21:

```jsx
sudo nmap -sV -p21 -sC -A 10.129.14.136
```

### Breakdown:

- `sV`: Version detection.
- `p21`: Scan port 21.
- `sC`: Run default NSE scripts.
- `A`: Aggressive scan (includes OS detection, version detection, script scanning, and traceroute).

### Output Highlights:

- **FTP service detected**: `vsftpd 2.0.8 or later`
- **Anonymous login allowed**
- Directories and files listed, many are `writeable` – potential for file upload!
- **ftp-anon** script detected anonymous login and dumped directory contents.
- **ftp-syst** script showed FTP status and server banner: `vsFTPd 3.0.3`

### **Script Tracing**

Use `--script-trace` to see **low-level interaction** between Nmap and the server:

```jsx
sudo nmap -sV -p21 -sC -A 10.129.14.136 --script-trace
```

This helps understand what NSE scripts are doing (like sending `STAT`, `USER`, `PASS` commands).

### **Manual FTP Interaction**

If you want to interact manually:

```jsx
nc -nv 10.129.14.136 21
```

or

```jsx
telnet 10.129.14.136 21
```

### **TLS/SSL Support (Encrypted FTP)**

If the FTP server uses TLS:

```jsx
openssl s_client -connect 10.129.14.136:21 -starttls ftp
```

This can show:

- Certificate details (e.g., CN = master.inlanefreight.htb)
- Organization, location, sometimes email address
- Useful for OSINT and identifying targets