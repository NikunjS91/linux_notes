# Linux Networking Commands

---

## Quick Reference Table

| Command | Purpose | Install Needed? |
|---------|---------|----------------|
| `ping` | Test connectivity | No |
| `traceroute` | Trace network hops | Sometimes |
| `mtr` | Ping + traceroute combined | Yes |
| `netstat` | Active connections & ports | Yes (`net-tools`) |
| `ss` | Modern replacement for netstat | No |
| `ifconfig` | Network interface info | Yes (`net-tools`) |
| `ip` | Modern replacement for ifconfig | No |
| `nslookup` | DNS lookup | No |
| `dig` | Detailed DNS lookup | Sometimes |
| `curl` | HTTP requests | No |
| `wget` | Download files | Sometimes |
| `nmap` | Port scanning | Yes |
| `iptables` | Firewall rules | No (needs root) |

---

## Connectivity & Diagnostics

### ping

Tests reachability of a host by sending ICMP packets and measuring round-trip time.

```bash
ping google.com

# Send only 4 packets then stop
ping -c 4 google.com

# Set interval between packets (0.5 seconds)
ping -i 0.5 google.com

# Ping with larger packet size (flood test)
ping -s 1024 google.com
```

**Reading ping output:**

| Field | Meaning |
|-------|---------|
| `time=` | Round-trip latency in milliseconds (lower is better) |
| `ttl=` | Time To Live — roughly shows how many hops away the host is |
| `packet loss` | % of packets that didn't return (0% is healthy, >5% is a problem) |
| `bytes` | Packet size sent (default 56 bytes) |
| `icmp_seq` | Sequence number — gaps indicate dropped packets |

---

### traceroute / tracepath

> May require installation: `sudo apt install traceroute`

Shows every router hop between your machine and the destination. Useful for diagnosing where a connection is slow or dropping.

```bash
traceroute google.com

# Use TCP instead of UDP (bypasses some firewalls)
traceroute -T google.com

# tracepath (no root needed, slightly less detail)
tracepath google.com
```

**Reading output:** Each row is one hop. `* * *` means that hop didn't respond (firewall/ICMP blocked — not necessarily broken).

---

### mtr (My Trace Route)

> Install: `sudo apt install mtr`

Combines `ping` + `traceroute` in a live, updating view. Best tool for diagnosing network path issues.

```bash
mtr google.com

# Non-interactive report mode (output once and exit)
mtr --report google.com

# Show IP addresses instead of hostnames
mtr -n google.com
```

---

## Network Interfaces & IP

### ip (modern, preferred)

```bash
# Show all interfaces and their IP addresses
ip address show
ip a              # shorthand

# Show only a specific interface
ip a show eth0

# Show routing table
ip route show
ip r

# Bring an interface up/down
ip link set eth0 up
ip link set eth0 down
```

---

### ifconfig (older, still common)

> Install: `sudo apt install net-tools`

```bash
# Show all interfaces
ifconfig

# Show specific interface
ifconfig eth0

# Assign a temporary IP address
ifconfig eth0 192.168.1.100 netmask 255.255.255.0
```

---

### iwconfig

> Install if needed: `sudo apt install wireless-tools`

Shows wireless network interface details — SSID, signal strength, bit rate.

```bash
iwconfig
iwconfig wlan0
```

---

### ifplugstatus

> Install: `sudo apt install ifplugd`

Shows whether each network interface cable is physically plugged in.

```bash
ifplugstatus
```

---

### hostname

```bash
hostname              # show machine hostname
hostname -I           # show all IP addresses
hostname -f           # show fully qualified domain name (FQDN)
```

---

## Active Connections & Ports

### ss (modern, preferred)

Faster and more feature-rich than `netstat`.

```bash
# Show all listening TCP and UDP ports
ss -tuln

# Show established connections
ss -t state established

# Show which process is using which port
ss -tlnp

# Find what's listening on port 80
ss -tlnp | grep :80
```

**Flag breakdown:**
| Flag | Meaning |
|------|---------|
| `-t` | TCP only |
| `-u` | UDP only |
| `-l` | Listening sockets only |
| `-n` | Show port numbers (no name resolution) |
| `-p` | Show process using the socket |

---

### netstat (older)

> Install: `sudo apt install net-tools`

```bash
# Show all listening ports
netstat -tuln

# Show all connections with process info
netstat -tulnp

# Show connection counts by state
netstat -an | awk '{print $6}' | sort | uniq -c | sort -rn
```

---

## DNS & Domain Lookup

### nslookup

Queries DNS to find the IP address for a hostname (or reverse lookup).

```bash
nslookup google.com

# Query a specific DNS server
nslookup google.com 8.8.8.8

# Reverse lookup (IP to hostname)
nslookup 142.250.80.46
```

---

### dig

More detailed DNS lookup tool — shows full DNS response with record types.

```bash
dig google.com

# Query for a specific record type
dig google.com A       # IPv4 address
dig google.com AAAA    # IPv6 address
dig google.com MX      # Mail exchange records
dig google.com NS      # Name server records
dig google.com TXT     # TXT records (SPF, DKIM, etc.)

# Short output (IP only)
dig +short google.com

# Query specific DNS server
dig @8.8.8.8 google.com

# Trace full DNS resolution path
dig +trace google.com
```

### DNS Record Types

| Record | Full Name | Purpose |
|--------|-----------|---------|
| `A` | Address | Maps domain → IPv4 address |
| `AAAA` | IPv6 Address | Maps domain → IPv6 address |
| `CNAME` | Canonical Name | Alias — points one domain to another |
| `MX` | Mail Exchange | Specifies mail servers for a domain |
| `NS` | Name Server | Delegates DNS zone to name servers |
| `TXT` | Text | Stores arbitrary text (SPF, DKIM, verification) |
| `PTR` | Pointer | Reverse DNS — maps IP → domain |
| `SOA` | Start of Authority | Zone metadata (TTL, admin email, serial) |
| `SRV` | Service | Specifies host + port for a service |

---

### whois

Shows domain registration info — registrar, owner, creation/expiry dates, name servers.

```bash
whois google.com
```

---

## Routing

### route (older)

```bash
route -n           # Show routing table with IP addresses
```

---

### arp

Discovers the MAC address associated with an IP on your local network.

```bash
arp -a             # Show ARP cache (all known hosts)
arp -n             # Numeric output (no hostname resolution)
```

---

## Daily Use Commands

### curl

Make HTTP/HTTPS requests from the command line. Supports GET, POST, headers, auth, and more.

### curl Common Flags

| Flag | Description |
|------|-------------|
| `-X METHOD` | Set HTTP method (GET, POST, PUT, DELETE, PATCH) |
| `-H "Header: val"` | Add a request header |
| `-d '{"key":"val"}'` | Send request body data |
| `-I` | Fetch response headers only (HEAD request) |
| `-L` | Follow redirects automatically |
| `-O` | Download file, keep original filename |
| `-o filename` | Download file, save as specified name |
| `-u user:pass` | HTTP basic authentication |
| `-k` | Skip SSL certificate verification |
| `-v` | Verbose — show full request and response |
| `-s` | Silent — suppress progress output |
| `-w "%{http_code}"` | Print HTTP status code after request |
| `\| jq` | Pipe to `jq` to pretty-print JSON response |

```bash
# Basic GET request
curl https://api.example.com/data

# Pretty-print JSON response
curl https://api.example.com/data | jq

# POST with JSON body
curl -X POST https://api.example.com/create \
  -H "Content-Type: application/json" \
  -d '{"name": "test"}'

# Include response headers in output
curl -I https://google.com

# Follow redirects
curl -L https://example.com

# Download a file
curl -O https://example.com/file.zip

# Pass Authorization header
curl -H "Authorization: Bearer TOKEN" https://api.example.com

# Show request and response details (verbose)
curl -v https://api.example.com
```

---

### wget

Downloads files from the internet. Better than `curl` for bulk/recursive downloads.

```bash
# Download a file
wget https://example.com/file.zip

# Download to a specific filename
wget -O myfile.zip https://example.com/file.zip

# Continue interrupted download
wget -c https://example.com/largefile.iso

# Download in background
wget -b https://example.com/largefile.iso

# Mirror an entire website
wget --mirror https://example.com
```

---

### telnet

Test TCP port connectivity to a remote host. Useful to check if a port is open.

```bash
telnet example.com 80
telnet example.com 443
telnet example.com 3306   # test MySQL port
```

> If the connection hangs or refuses, the port is closed or firewalled. If you get a blank prompt or response, the port is open.

---

### nc (netcat)

The "Swiss Army knife" of networking — test ports, transfer files, create simple servers.

```bash
# Test if a port is open (-z = scan, -v = verbose)
nc -zv example.com 80

# Test a range of ports
nc -zv example.com 80-90

# Simple TCP server on port 1234
nc -l 1234

# Send a file over the network
# Receiver: nc -l 9999 > received_file.txt
# Sender:   nc receiver_ip 9999 < file.txt
```

---

### nmap

Network scanner — discover hosts and open ports on a network.

```bash
# Basic port scan
nmap example.com

# Scan a specific port
nmap -p 80 example.com

# Scan a range of ports
nmap -p 1-1000 example.com

# Detect OS and service versions
nmap -A example.com

# Scan an entire subnet
nmap 192.168.1.0/24

# Fast scan (most common ports only)
nmap -F example.com
```

> **Note:** Only scan systems you own or have permission to scan.

---

### watch

Run any command repeatedly and see it refresh in place.

```bash
# Run 'df -h' every 2 seconds (default)
watch df -h

# Refresh every 5 seconds
watch -n 5 df -h

# Highlight differences between refreshes
watch -d -n 2 df -h

# Watch active connections update
watch -n 1 ss -tuln
```

---

### iptables

Manages Linux firewall rules (packet filtering).

> Requires root: `sudo`

```bash
# List all rules
sudo iptables -L

# List with line numbers and no name resolution
sudo iptables -L -n --line-numbers

# Allow incoming traffic on port 80
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Block an IP address
sudo iptables -A INPUT -s 192.168.1.100 -j DROP

# Delete a rule by line number
sudo iptables -D INPUT 3

# Save rules so they persist after reboot (Debian/Ubuntu)
sudo iptables-save > /etc/iptables/rules.v4
```
