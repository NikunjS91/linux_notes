# Linux Networking Commands

---

## Connectivity & Diagnostics

### ping
Sends and receives packets to/from a server to check connection status.
```bash
ping google.com
```

### traceroute / tracepath
> Requires installation

Shows the hops a request takes from your local machine to a destination address.
```bash
traceroute google.com
tracepath google.com
```

### mtr (My Trace Route)
Combines `ping` and `traceroute` — shows both path and latency in real time.
```bash
mtr google.com
```

---

## Network Information

### netstat
> Requires `net-tools` package to be installed

Shows active connections, sockets, and listening ports on the server.
```bash
netstat -tuln
```

### ss
Same functionality as `netstat` — modern replacement.
```bash
ss -tuln
```

### ifconfig
Shows all network interface configurations on the server.
```bash
ifconfig
```

### ip
Modern tool to view IP address details.
```bash
ip address show
```

### iwconfig
> Requires installation

Shows **wireless** network interface configurations.
```bash
iwconfig
```

### ifplugstatus
> Requires installation

Shows which network interfaces are physically connected/active.
```bash
ifplugstatus
```

### hostname
Shows the machine's IP address or hostname.
```bash
hostname
hostname -I   # shows IP address
```

---

## DNS & Domain Lookup

### nslookup
Shows which IP address is active on a particular DNS name.
```bash
nslookup google.com
```

### dig
Shows where a website is hosted (DNS records).
```bash
dig google.com
```

### whois
Provides domain registration and DNS details for a website.
```bash
whois google.com
```

---

## Routing

### route
Shows the routing table — how internet traffic is being routed.
```bash
route -n
```

### arp
Queries the router to find the MAC address for an IP.
```bash
arp -a
```

---

## Daily Use Commands

### curl
Calls HTTP/HTTPS endpoints.
```bash
# Basic GET request
curl -X GET https://api.example.com/endpoint

# Pretty-print JSON output
curl -X GET https://api.example.com/endpoint | jq
```

### wget
Download files from the internet.
```bash
wget https://example.com/file.zip
```

### telnet
Similar to `nslookup` but also lets you specify a port (useful for testing port connectivity).
```bash
telnet example.com 80
```

### nc (netcat)
Versatile networking tool — port scanning, transferring data, testing connections.
```bash
nc -zv example.com 80
```

### nmap
Scan a website or server to check for open ports.
```bash
nmap example.com
```

### watch
Run a command repeatedly and see its output update every N seconds (default: 2s).
```bash
# Run 'top' every 5 seconds
watch -n 5 top

# Watch disk usage update
watch -n 2 df -h
```

### iptables
> Requires root permissions

Manage IP tables (firewall rules and IP information).
```bash
sudo iptables -L   # list rules
```
