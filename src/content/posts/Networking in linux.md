---
title: "Networking in Linux"
published: 2026-03-06
description: "Core concepts about networking in Linux and how it works."
# image: "../../assets/images/my_photo.jpg"
tags: ["Linux", "Networking", "DevOps", "TCP/IP", "DSO101"]
category: DevOps
draft: false
---

## Introduction

Linux networking is the set of tools, kernel features, and configuration methods that allow a Linux system to communicate over a network. It covers interface setup, IP addressing, routing, DNS, firewall rules, packet inspection, and troubleshooting.

### Why Networking Matters

Networking is critical because almost every modern Linux machine depends on it for:

- Internet access
- File sharing
- Remote login
- Web services
- Cloud applications
- Container communication

A solid understanding of Linux networking helps you manage systems and solve connection problems faster.

---

## Basic Idea

A Linux computer can act as a client, server, router, firewall, or container host, depending on how its network is configured. The networking stack in Linux is built into the kernel, and user-space tools are used to view and change the configuration.

---

## Network Interfaces

### What is a Network Interface?

A network interface is how Linux connects to a network. It may be:

- **Physical** — Ethernet card, Wi-Fi adapter, etc.
- **Virtual** — Loopback (lo), bridge, tunnel interfaces, etc.

### Loopback Interface

The loopback interface, usually `lo`, is used for local communication inside the machine itself. It always has the address `127.0.0.1` (IPv4) or `::1` (IPv6).

### Viewing Interfaces

You can view interfaces with:

```bash
# Modern way (preferred)
ip address show
ip a

# View specific interface
ip a show eth0

# View interface status
ip link show

# Bring interface up
ip link set eth0 up

# Bring interface down
ip link set eth0 down
```

---

## IP Addressing

### What is an IP Address?

An IP address identifies a device on a network. Linux supports both **IPv4** and **IPv6**, and each interface may have one or more addresses assigned to it.

### Address Structure

The address includes a prefix length, such as `/24`, which defines the network portion of the address.

### Examples

```
IPv4: 192.168.1.100/24
      └─ Address      └─ Prefix (network portion)

IPv6: 2001:db8::1/64
      └─ Address    └─ Prefix
```

### How Prefix Length Works

When you assign an address, Linux can automatically understand which devices are on the same local network based on the prefix length:

- `/24` = First 24 bits are network, last 8 bits are host (254 hosts per subnet)
- `/16` = First 16 bits are network (65,534 hosts per subnet)
- `/32` = Entire address is network (single host)

### Assigning an IP Address

```bash
# Add an IP address to an interface
ip address add 192.168.1.100/24 dev eth0

# Remove an IP address
ip address delete 192.168.1.100/24 dev eth0

# View all addresses
ip a show
```

### Impact of Wrong Addressing

If the address is wrong, connectivity problems usually appear immediately because packets cannot be delivered to the correct destination.

---

## Routing

### What is Routing?

Routing tells Linux where to send packets that are not on the local network. Linux keeps this information in a routing table.

### Viewing the Routing Table

```bash
# View IPv4 routes
ip route show

# View IPv6 routes
ip -6 route show

# View detailed route information
ip route list

# View a specific route
ip route show to 192.168.1.0/24
```

### Gateway and Next Hop

A route may point to a gateway, which is the next hop used to reach another network. The gateway is typically the IP address of the router on your local network.

### Default Route

The default route is used when Linux does not have a more specific route for a destination:

- IPv4: `0.0.0.0/0`
- IPv6: `::/0`

### Adding Routes

```bash
# Add a static route
ip route add 10.0.0.0/8 via 192.168.1.1 dev eth0

# Set a default gateway
ip route add default via 192.168.1.1

# Delete a route
ip route delete 10.0.0.0/8 via 192.168.1.1
```

### Why Routing is Essential

Routing is essential for reaching the internet or any network outside the local subnet. Without proper routing, packets to remote hosts will never reach their destination.

---

## DNS (Domain Name System)

### What is DNS?

DNS, or Domain Name System, translates human-readable names like `example.com` into IP addresses. Without DNS, you would need to remember numeric addresses for every server, which is inconvenient and error-prone.

### How Linux Uses DNS

Linux systems use DNS settings to resolve names for:

- Browsers
- Package managers
- Remote services
- Internal applications

### Checking DNS Configuration

```bash
# View DNS servers
cat /etc/resolv.conf

# View nameserver settings
systemctl status systemd-resolved

# Test DNS resolution
nslookup example.com
dig example.com
host example.com
```

### Testing DNS Resolution

```bash
# Query a specific nameserver
nslookup google.com 8.8.8.8

# Get detailed DNS information
dig example.com

# Reverse DNS lookup
dig -x 8.8.8.8
```

### Common DNS Issues

DNS problems are common in troubleshooting because a machine may have internet connectivity but still fail to reach websites by name. In such cases, the issue is often name resolution rather than routing or link failure.

---

## Common Networking Commands

### Essential Commands

| Command | Purpose |
|---------|---------|
| `ip a` or `ip address show` | Display network interfaces |
| `ip route show` | Display routing table |
| `ping host` | Test packet delivery and latency |
| `ss -tuln` | View listening ports and connections |
| `traceroute host` | Show the path packets take to destination |
| `netstat` | Display network connections (older tool) |
| `ifconfig` | Configure interfaces (older tool) |

### Modern Commands (Recommended)

```bash
# Display all interfaces
ip a

# Display routes
ip route show

# Test reachability
ping -c 4 8.8.8.8

# View active connections
ss -tuln

# View established connections
ss -tan

# Trace path to host
traceroute example.com

# Resolve a hostname
nslookup example.com

# Detailed DNS query
dig example.com
```

### Older Commands (Legacy)

```bash
# These still work but are deprecated
ifconfig
route
netstat
nslookup
```

---

## Firewall and Security

### What is a Firewall?

Firewalls control which traffic is allowed or blocked on a Linux system. Linux systems often use **iptables** or **nftables** to define packet filtering rules.

### Purpose of Firewalls

Firewall rules can:

- Allow traffic on one port
- Block traffic on another port
- Filter traffic by protocol, source, destination, or interface
- Protect servers from unwanted access
- Restrict services to trusted networks

### Firewall Tools

| Tool | Type | Status |
|------|------|--------|
| **iptables** | Packet filter | Traditional, widely used |
| **nftables** | Packet filter | Modern replacement for iptables |
| **firewalld** | Firewall manager | User-friendly, uses nftables |
| **ufw** | Firewall tool | Simpler syntax (Ubuntu/Debian) |

### UFW Examples (Simpler Syntax)

```bash
# Enable firewall
sudo ufw enable

# Allow SSH
sudo ufw allow 22/tcp

# Allow HTTP and HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Block a port
sudo ufw deny 3306/tcp

# View firewall status
sudo ufw status

# Delete a rule
sudo ufw delete allow 22/tcp
```

### IPTables Examples

```bash
# Allow traffic on port 80
iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Block traffic from a specific IP
iptables -A INPUT -s 192.168.1.100 -j DROP

# Allow established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# View current rules
iptables -L

# Save rules
iptables-save > /etc/iptables/rules.v4
```

### Why Firewall Security Matters

Security is a major part of Linux networking because even a correctly configured network can still be unsafe if firewall policies are missing or too open.

---

## Network Services

### What are Network Services?

Linux networking also includes services that support communication and system management:

- **SSH** — Remote login and management
- **HTTP/HTTPS** — Web servers for hosting pages
- **FTP/SFTP** — File transfer services
- **DNS** — Name resolution services
- **DHCP** — IP address assignment
- **NFS** — Network file sharing
- **Samba** — Windows file sharing

### Ports and Services

A **port** identifies a specific service on a machine. For example, a server may have one IP address but several services running on different ports:

| Port | Service | Protocol |
|------|---------|----------|
| 22 | SSH | TCP |
| 80 | HTTP | TCP |
| 443 | HTTPS | TCP |
| 53 | DNS | TCP/UDP |
| 3306 | MySQL | TCP |
| 5432 | PostgreSQL | TCP |
| 6379 | Redis | TCP |

### Checking Open Ports

```bash
# View listening ports (all)
ss -tuln

# View listening ports (specific protocol)
ss -tln

# View established connections
ss -tan

# View specific service port
ss -tln | grep :22
```

### Starting a Service

```bash
# Start a service
sudo systemctl start ssh

# Enable at boot
sudo systemctl enable ssh

# Check status
sudo systemctl status ssh

# Stop a service
sudo systemctl stop ssh
```

### Understanding Ports

Understanding ports is important when checking why a service is unreachable. A service may be running but not listening on the expected port, or the firewall may be blocking access.

---

## Virtual Networking

### What is Virtual Networking?

Linux is widely used in virtual machines and containers, so virtual networking is a big part of the topic. Linux can create:

- **Bridges** — Connect multiple interfaces as if on same network
- **Virtual Ethernet pairs (veth)** — Connect containers or VMs
- **NAT setups** — Network address translation for private networks
- **Tunnels** — Encrypted connections to remote networks
- **VLANs** — Logical network separation

### Container Networking

In container environments, traffic is often handled through:

- **Bridging** — Containers on same bridge can communicate
- **NAT** — Containers can access external networks through host
- **Overlay networks** — Multi-host container communication

### Why Virtual Networking Matters

Virtual networking is important for:

- **Cloud platforms** — Running multiple virtual machines
- **Lab setups** — Testing network configurations safely
- **Advanced server administration** — Complex network designs
- **Container orchestration** — Managing container communication

### Creating a Bridge

```bash
# Create a bridge interface
ip link add br0 type bridge

# Bring it up
ip link set br0 up

# Add an interface to the bridge
ip link set eth0 master br0

# Assign an IP to the bridge
ip address add 192.168.1.100/24 dev br0
```

---

## Troubleshooting Flow

### Step-by-Step Troubleshooting

A practical way to troubleshoot Linux networking is to check the problem step by step:

1. **Confirm the interface is up** — `ip link show` or `ip a show`
2. **Verify the IP address** — `ip a show` or check for correct subnet
3. **Confirm the route** — `ip route show` and check default gateway
4. **Test DNS** — `nslookup` or `dig` to resolve names
5. **Inspect the firewall** — Check `ufw status` or `iptables -L`

### Troubleshooting Examples

#### Example 1: Ping numeric address works, hostname fails

```bash
ping 8.8.8.8      # This works
ping google.com   # This fails
```

**Conclusion:** Network path is fine, but DNS may be broken.

**Next steps:**

```bash
# Test DNS
nslookup google.com

# Check DNS configuration
cat /etc/resolv.conf

# Test with specific nameserver
nslookup google.com 8.8.8.8
```

#### Example 2: All pings fail

```bash
ping 8.8.8.8   # Fails
```

**Conclusion:** Issue may be with interface, gateway, or routing table.

**Next steps:**

```bash
# Check interface status
ip link show

# Check IP address
ip a show

# Check routes
ip route show

# Check if interface is up
ip link set eth0 up

# Verify gateway
ip route show | grep default
```

#### Example 3: Local network fails, external works

```bash
ping 192.168.1.1  # Fails
ping 8.8.8.8      # Works
```

**Conclusion:** Issue with local network, likely subnet mask or interface.

**Next steps:**

```bash
# Check local interface
ip a show

# Check subnet mask is correct
ip route show | grep 192.168.1

# Ping gateway
ping 192.168.1.1
```

---

## Important Kernel Ideas

### How Linux Networking Works Under the Hood

Under the hood, Linux networking is handled by the kernel networking stack:

- **Network devices** — Represented in the kernel
- **Socket buffers** — Structures for moving packets through layers
- **Sockets** — How applications send and receive data
- **Kernel routing** — Kernel manages packet delivery, interface handling, and routing

### Why This Matters

This kernel-based design is one reason Linux networking is powerful and efficient. It also explains why system administrators often need both:

- User-space commands (for configuration)
- Kernel-level understanding (for debugging complex issues)

---

## Quick Reference Summary

### The Five Core Concepts

| Concept | Definition | Example |
|---------|-----------|---------|
| **Interface** | The network connection point | `eth0`, `wlan0`, `lo` |
| **IP Address** | Device identity on the network | `192.168.1.100/24` |
| **Route** | Path used to reach another network | `default via 192.168.1.1` |
| **DNS** | Name to address translation | `google.com` → `8.8.8.8` |
| **Firewall** | Traffic control and protection | Allow/block ports and services |

### Most Important Commands

```bash
# Check interface and IP
ip a

# Check routing table
ip route show

# Test connectivity
ping 8.8.8.8
ping google.com

# Check open ports
ss -tuln

# Inspect firewall
ufw status

# Trace packet path
traceroute example.com
```

---

## Exam Quick Notes

Linux networking is the process of connecting Linux systems to each other and to external networks using:

- **Interfaces** — The physical or virtual connection points
- **IP addresses** — Device identities with prefix length
- **Routing** — Paths to reach different networks
- **DNS** — Translation from names to addresses
- **Firewalls** — Traffic control and security rules

### Key Takeaways

- The most commonly used configuration command is **`ip`**
- The most common troubleshooting tools are **`ping`**, **`ss`**, and **`traceroute`**
- Always troubleshoot in order: Interface → IP → Route → DNS → Firewall
- Virtual networking is critical for containers and cloud platforms
- Linux networking is kernel-based and very efficient

---

## Summary

Linux networking is fundamental to modern system administration. By mastering interfaces, IP addressing, routing, DNS, and firewalls, you can:

- Configure systems for internet connectivity
- Troubleshoot connection problems efficiently
- Set up secure firewall policies
- Manage virtual and container networks
- Support cloud and enterprise infrastructure
