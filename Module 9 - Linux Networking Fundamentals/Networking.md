# Module 9: Linux Networking Fundamentals

## Overview
Networking is at the heart of every DevOps workflow — from deploying applications and configuring servers to managing cloud infrastructure and securing communication between services. This module covers the essential Linux networking concepts, commands, and tools every DevOps engineer must know.

---

## 9.1 Networking Concepts Refresher

Before diving into commands, here are the key concepts you need to understand:

| Concept | Description |
|---------|-------------|
| **IP Address** | Unique identifier for a device on a network (e.g., `192.168.1.10`) |
| **Subnet Mask** | Defines the network and host portion of an IP (e.g., `255.255.255.0` or `/24`) |
| **Gateway** | Router IP that connects your network to other networks |
| **DNS** | Translates domain names to IP addresses (e.g., `google.com` → `142.250.80.46`) |
| **Port** | Logical endpoint for a service (e.g., HTTP=80, HTTPS=443, SSH=22) |
| **MAC Address** | Hardware address of a network interface (e.g., `00:1A:2B:3C:4D:5E`) |
| **CIDR** | Notation for IP ranges (e.g., `10.0.0.0/16` = 65,536 addresses) |
| **Loopback** | Virtual interface for local communication (`127.0.0.1` or `localhost`) |

---

## 9.2 Viewing Network Interfaces and IP Addresses

### `ip` — Modern Network Configuration Tool
The `ip` command is the modern replacement for the deprecated `ifconfig`.

```bash
ip addr                          # Show all interfaces and IP addresses
ip addr show eth0                # Show details for a specific interface
ip link                          # Show network interfaces (link layer)
ip link show eth0                # Show a specific interface status
ip -4 addr                       # Show IPv4 addresses only
ip -6 addr                       # Show IPv6 addresses only
```

**Example output of `ip addr`:**
```
1: lo: <LOOPBACK,UP> mtu 65536
    inet 127.0.0.1/8 scope host lo
2: eth0: <BROADCAST,MULTICAST,UP> mtu 1500
    inet 192.168.1.10/24 brd 192.168.1.255 scope global eth0
    inet6 fe80::1/64 scope link
```

---

### `ifconfig` — Legacy Network Interface Tool
Still available on many systems, useful to know.

```bash
ifconfig                         # Show all active interfaces
ifconfig eth0                    # Show a specific interface
ifconfig eth0 up                 # Bring interface up
ifconfig eth0 down               # Bring interface down
```

> Install with: `sudo apt install net-tools` (Ubuntu/Debian)

---

## 9.3 Routing and Gateway

### `ip route` — View and Manage Routing Table

```bash
ip route                         # Show routing table
ip route show                    # Same as above
ip route get 8.8.8.8             # Show which route is used to reach an IP
```

**Example output:**
```
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
```

### Adding and Removing Routes

```bash
sudo ip route add 10.0.0.0/8 via 192.168.1.1       # Add a static route
sudo ip route del 10.0.0.0/8                        # Remove a route
sudo ip route add default via 192.168.1.1           # Set default gateway
```

---

## 9.4 DNS Configuration and Resolution

### `/etc/resolv.conf` — DNS Server Configuration

```bash
cat /etc/resolv.conf             # View configured DNS servers
```

**Example:**
```
nameserver 8.8.8.8
nameserver 8.8.4.4
search example.com
```

### `/etc/hosts` — Local Hostname Resolution

```bash
cat /etc/hosts                   # View local hostname mappings
sudo nano /etc/hosts             # Edit local DNS entries
```

**Example entry:**
```
127.0.0.1   localhost
192.168.1.20  myserver.local myserver
```

> Entries in `/etc/hosts` take priority over DNS — useful for local development and testing.

---

### `nslookup` — Query DNS Records

```bash
nslookup google.com              # Resolve a domain name
nslookup google.com 8.8.8.8      # Use a specific DNS server
nslookup -type=MX google.com     # Query MX (mail) records
nslookup -type=A google.com      # Query A (IPv4) records
```

---

### `dig` — Advanced DNS Lookup Tool

```bash
dig google.com                   # Full DNS query output
dig google.com A                 # Query A record
dig google.com MX                # Query MX record
dig google.com +short            # Short output (IP only)
dig @8.8.8.8 google.com          # Use a specific DNS server
dig -x 142.250.80.46             # Reverse DNS lookup (IP to hostname)
```

**Example output:**
```
;; ANSWER SECTION:
google.com.     299   IN  A   142.250.80.46
```

> `dig` is preferred over `nslookup` for scripting and detailed DNS troubleshooting.

---

## 9.5 Testing Connectivity

### `ping` — Test Network Reachability

```bash
ping google.com                  # Ping continuously (Ctrl+C to stop)
ping -c 4 google.com             # Send exactly 4 packets
ping -i 2 google.com             # Ping every 2 seconds
ping -s 1000 google.com          # Send packets of 1000 bytes
ping 192.168.1.1                 # Ping a local gateway
```

**Example output:**
```
PING google.com (142.250.80.46): 56 bytes of data
64 bytes from 142.250.80.46: icmp_seq=0 ttl=117 time=12.3 ms
64 bytes from 142.250.80.46: icmp_seq=1 ttl=117 time=11.8 ms
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss
```

---

### `traceroute` / `tracepath` — Trace Network Path

```bash
traceroute google.com            # Trace the route to a host
tracepath google.com             # Similar, no root required
traceroute -n google.com         # Skip DNS resolution (faster)
```

> Use `traceroute` to identify where network latency or packet loss is occurring.

---

### `mtr` — Combined ping + traceroute (Real-time)

```bash
mtr google.com                   # Interactive real-time network path analysis
mtr --report google.com          # Generate a report and exit
mtr -n google.com                # No DNS resolution
```

> `mtr` is the best tool for diagnosing intermittent network issues.

---

## 9.6 Port and Socket Inspection

### `ss` — Socket Statistics (Modern `netstat`)

```bash
ss -tuln                         # Show all listening TCP/UDP ports
ss -tulnp                        # Include process name and PID
ss -s                            # Summary of socket statistics
ss -t state established          # Show established TCP connections
ss -an | grep :80                # Check if port 80 is in use
```

**Example output of `ss -tulnp`:**
```
Netid  State   Recv-Q  Send-Q  Local Address:Port   Process
tcp    LISTEN  0       128     0.0.0.0:22            sshd
tcp    LISTEN  0       511     0.0.0.0:80            nginx
tcp    LISTEN  0       128     0.0.0.0:443           nginx
```

---

### `netstat` — Legacy Socket Tool

```bash
netstat -tuln                    # Show listening ports
netstat -tulnp                   # Include process info (requires sudo)
netstat -an                      # All connections and listening ports
netstat -rn                      # Show routing table
```

> Install with: `sudo apt install net-tools`

---

### `lsof` — List Open Files and Network Connections

```bash
sudo lsof -i                     # Show all network connections
sudo lsof -i :80                 # Show what is using port 80
sudo lsof -i :22                 # Show what is using port 22
sudo lsof -i TCP                 # Show all TCP connections
sudo lsof -i -n -P               # No DNS/port name resolution
```

---

### `nmap` — Network Port Scanner

```bash
nmap localhost                   # Scan open ports on localhost
nmap 192.168.1.1                 # Scan a specific host
nmap -p 80,443,22 192.168.1.1    # Scan specific ports
nmap -sV 192.168.1.1             # Detect service versions
nmap 192.168.1.0/24              # Scan an entire subnet
```

> Install with: `sudo apt install nmap`
> Use `nmap` for network discovery and security auditing — always get permission before scanning.

---

## 9.7 Downloading and Transferring Files

### `curl` — Transfer Data from URLs

```bash
curl https://example.com                        # Fetch a webpage
curl -o file.tar.gz https://example.com/file.tar.gz   # Download and save
curl -I https://example.com                     # Show HTTP headers only
curl -L https://example.com                     # Follow redirects
curl -X POST -d '{"key":"value"}' -H "Content-Type: application/json" https://api.example.com
curl -u user:password https://example.com       # Basic authentication
curl -k https://self-signed.example.com         # Skip SSL verification
```

---

### `wget` — Download Files from the Web

```bash
wget https://example.com/file.tar.gz            # Download a file
wget -O myfile.tar.gz https://example.com/file  # Save with custom name
wget -c https://example.com/largefile.tar.gz    # Resume interrupted download
wget -q https://example.com/file.tar.gz         # Quiet mode (no output)
wget --mirror https://example.com               # Mirror an entire website
```

---

### `scp` — Secure Copy Over SSH

```bash
# Copy local file to remote server
scp file.txt user@192.168.1.10:/home/user/

# Copy remote file to local machine
scp user@192.168.1.10:/home/user/file.txt /local/path/

# Copy entire directory recursively
scp -r /local/dir user@192.168.1.10:/remote/dir/

# Use a specific SSH key
scp -i ~/.ssh/mykey.pem file.txt ec2-user@<ec2-ip>:/home/ec2-user/
```

---

### `rsync` — Efficient File Synchronization

```bash
rsync -av /local/dir/ user@192.168.1.10:/remote/dir/    # Sync local to remote
rsync -av user@192.168.1.10:/remote/dir/ /local/dir/    # Sync remote to local
rsync -avz /local/dir/ user@host:/remote/dir/           # Compress during transfer
rsync -av --delete /local/dir/ user@host:/remote/dir/   # Delete files not in source
rsync -av --progress /local/dir/ user@host:/remote/dir/ # Show progress
rsync -av -e "ssh -i ~/.ssh/mykey.pem" /dir/ ec2-user@<ec2-ip>:/dir/  # With SSH key
```

> `rsync` is preferred over `scp` for large transfers — it only transfers changed files.

---

## 9.8 SSH — Secure Shell

SSH is the primary tool for remote server access in DevOps.

### Connecting to a Remote Server

```bash
ssh user@192.168.1.10                        # Connect with password
ssh -i ~/.ssh/mykey.pem ec2-user@<ec2-ip>    # Connect with SSH key (AWS EC2)
ssh -p 2222 user@192.168.1.10                # Connect on a non-default port
ssh -v user@192.168.1.10                     # Verbose mode (debug connection issues)
```

### SSH Key Management

```bash
# Generate an SSH key pair
ssh-keygen -t rsa -b 4096 -C "your@email.com"

# Copy public key to remote server (enables passwordless login)
ssh-copy-id user@192.168.1.10

# Manually add public key to remote server
cat ~/.ssh/id_rsa.pub | ssh user@host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"

# View your public key
cat ~/.ssh/id_rsa.pub
```

### SSH Config File (`~/.ssh/config`)
Simplifies SSH connections with aliases.

```bash
nano ~/.ssh/config
```

**Example config:**
```
Host myserver
    HostName 192.168.1.10
    User mike
    Port 22
    IdentityFile ~/.ssh/mykey.pem

Host prod-ec2
    HostName 54.123.45.67
    User ec2-user
    IdentityFile ~/.ssh/aws-key.pem
```

Now connect with just:
```bash
ssh myserver
ssh prod-ec2
```

### SSH Tunneling (Port Forwarding)

```bash
# Local port forwarding — access remote service locally
ssh -L 8080:localhost:80 user@192.168.1.10
# Now access http://localhost:8080 to reach port 80 on the remote server

# Remote port forwarding — expose local service to remote server
ssh -R 9090:localhost:3000 user@192.168.1.10

# Dynamic port forwarding — SOCKS proxy
ssh -D 1080 user@192.168.1.10
```

---

## 9.9 Firewall Management

### `ufw` — Uncomplicated Firewall (Ubuntu/Debian)

```bash
sudo ufw status                  # Check firewall status
sudo ufw enable                  # Enable firewall
sudo ufw disable                 # Disable firewall
sudo ufw allow 22                # Allow SSH
sudo ufw allow 80                # Allow HTTP
sudo ufw allow 443               # Allow HTTPS
sudo ufw allow 8080/tcp          # Allow specific port/protocol
sudo ufw deny 23                 # Block Telnet
sudo ufw delete allow 80         # Remove a rule
sudo ufw allow from 192.168.1.0/24 to any port 22   # Allow SSH from subnet only
sudo ufw status verbose          # Detailed status with rules
```

---

### `firewalld` — Firewall for RHEL/CentOS

```bash
sudo systemctl start firewalld   # Start firewalld
sudo firewall-cmd --state        # Check if running
sudo firewall-cmd --list-all     # List all rules

# Allow services
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --permanent --add-service=ssh

# Allow specific ports
sudo firewall-cmd --permanent --add-port=8080/tcp

# Remove a rule
sudo firewall-cmd --permanent --remove-service=http

# Apply changes
sudo firewall-cmd --reload
```

---

### `iptables` — Low-level Firewall Rules

```bash
sudo iptables -L                 # List all rules
sudo iptables -L -n -v           # Verbose with packet counts
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT    # Allow SSH
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT    # Allow HTTP
sudo iptables -A INPUT -j DROP   # Drop all other inbound traffic
sudo iptables -D INPUT -p tcp --dport 80 -j ACCEPT    # Delete a rule
sudo iptables-save > /etc/iptables/rules.v4            # Save rules
```

> For most use cases, prefer `ufw` (Ubuntu) or `firewalld` (RHEL) over raw `iptables`.

---

## 9.10 Network Configuration Files

| File | Purpose |
|------|---------|
| `/etc/hosts` | Local hostname to IP mappings |
| `/etc/resolv.conf` | DNS server configuration |
| `/etc/hostname` | System hostname |
| `/etc/network/interfaces` | Network config (Debian/Ubuntu legacy) |
| `/etc/netplan/*.yaml` | Network config (Ubuntu 18.04+) |
| `/etc/sysconfig/network-scripts/ifcfg-eth0` | Network config (RHEL/CentOS) |

### Viewing and Setting Hostname

```bash
hostname                         # Show current hostname
hostnamectl                      # Show detailed hostname info
sudo hostnamectl set-hostname myserver   # Set a new hostname
```

### Netplan Configuration (Ubuntu 18.04+)

```bash
cat /etc/netplan/*.yaml          # View current network config
sudo nano /etc/netplan/00-installer-config.yaml
```

**Example static IP config:**
```yaml
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 192.168.1.10/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

```bash
sudo netplan apply               # Apply network changes
```

---

## 9.11 Network Troubleshooting Workflow

When facing a network issue, follow this systematic approach:

```
1. Is the interface up?
   → ip addr / ip link

2. Do I have an IP address?
   → ip addr show eth0

3. Can I reach the gateway?
   → ping <gateway-ip>

4. Can I reach the internet?
   → ping 8.8.8.8

5. Is DNS working?
   → ping google.com
   → dig google.com

6. Is the service listening on the right port?
   → ss -tulnp | grep <port>

7. Is the firewall blocking the port?
   → sudo ufw status
   → sudo iptables -L

8. Is the route correct?
   → ip route
   → traceroute <destination>
```

---

## 9.12 Practical Lab Exercises

### Exercise 1: Network Inspection
```bash
# 1. View all network interfaces and IPs
ip addr

# 2. Check the routing table
ip route

# 3. View DNS configuration
cat /etc/resolv.conf

# 4. Check all listening ports
ss -tulnp
```

### Exercise 2: Connectivity Testing
```bash
# 1. Ping the gateway (replace with your gateway IP)
ping -c 4 192.168.1.1

# 2. Ping a public DNS server
ping -c 4 8.8.8.8

# 3. Resolve a domain name
dig google.com +short

# 4. Trace the route to google.com
traceroute google.com
```

### Exercise 3: SSH Key Setup
```bash
# 1. Generate an SSH key pair
ssh-keygen -t rsa -b 4096

# 2. Copy the public key to a remote server
ssh-copy-id user@<remote-ip>

# 3. Connect without a password
ssh user@<remote-ip>
```

### Exercise 4: Firewall Rules (Ubuntu)
```bash
# 1. Check firewall status
sudo ufw status

# 2. Allow SSH and HTTP
sudo ufw allow 22
sudo ufw allow 80

# 3. Enable the firewall
sudo ufw enable

# 4. Verify rules
sudo ufw status verbose
```

---

## 9.13 Quick Reference Cheat Sheet

| Command | Description |
|---------|-------------|
| `ip addr` | Show all IP addresses |
| `ip route` | Show routing table |
| `ip link` | Show network interfaces |
| `ping -c 4 host` | Test connectivity (4 packets) |
| `traceroute host` | Trace network path |
| `mtr host` | Real-time network path analysis |
| `dig domain +short` | Quick DNS lookup |
| `nslookup domain` | DNS query |
| `ss -tulnp` | Show listening ports with process |
| `lsof -i :80` | What is using port 80 |
| `nmap localhost` | Scan open ports |
| `curl -I https://url` | Check HTTP response headers |
| `wget url` | Download a file |
| `scp file user@host:/path` | Secure copy to remote |
| `rsync -av src/ user@host:/dst/` | Sync files to remote |
| `ssh -i key.pem user@host` | SSH with key file |
| `ssh-keygen -t rsa -b 4096` | Generate SSH key pair |
| `ssh-copy-id user@host` | Copy public key to server |
| `sudo ufw allow 80` | Allow port 80 in firewall |
| `sudo ufw status` | Check firewall rules |
| `cat /etc/hosts` | View local DNS mappings |
| `hostname` | Show system hostname |
| `sudo netplan apply` | Apply network config changes |

---

## Summary

In this module, you learned:
- Core networking concepts: IP, DNS, ports, gateways, and CIDR
- How to inspect network interfaces and IPs with `ip addr` and `ifconfig`
- How to view and manage routing tables with `ip route`
- How to configure and troubleshoot DNS using `/etc/resolv.conf`, `dig`, and `nslookup`
- How to test connectivity with `ping`, `traceroute`, and `mtr`
- How to inspect open ports and sockets with `ss`, `netstat`, and `lsof`
- How to transfer files securely with `scp` and `rsync`
- How to manage SSH connections, keys, config files, and tunneling
- How to configure firewalls with `ufw`, `firewalld`, and `iptables`
- A systematic approach to network troubleshooting

---

> **Next Module →** Module 10: Linux Security & Hardening
