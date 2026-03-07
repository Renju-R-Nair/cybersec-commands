# 🦈 Wireshark — GUI Packet Analysis

Wireshark is the world's most widely used network protocol analyzer with a graphical interface. It can open `.pcap` files captured by `tcpdump` or capture live traffic directly.

---

## Installation

```bash
# Ubuntu/Debian
sudo apt install wireshark

# CentOS/RHEL
sudo yum install wireshark-gnome

# macOS (Homebrew)
brew install --cask wireshark

# Windows
# Download from: https://www.wireshark.org/download.html
```

---

## Display Filters (GUI Filter Bar)

These are typed into the filter bar at the top of the Wireshark window.

### Protocol Filters

```
# Filter by protocol
tcp
udp
icmp
dns
http
https
arp
ftp
ssh
smb
```

### IP Address Filters

```
# Traffic to/from a specific IP
ip.addr == 192.168.1.1

# Traffic from source IP
ip.src == 192.168.1.1

# Traffic to destination IP
ip.dst == 192.168.1.1

# Exclude an IP
ip.addr != 192.168.1.1
```

### Port Filters

```
# Filter by port
tcp.port == 443
udp.port == 53

# Source port
tcp.srcport == 80

# Destination port
tcp.dstport == 22
```

### Combining Filters

```
# AND
ip.addr == 192.168.1.1 && tcp.port == 80

# OR
tcp.port == 80 || tcp.port == 443

# NOT
!arp

# Complex
ip.src == 192.168.1.0/24 && tcp.dstport == 443 && !ip.dst == 8.8.8.8
```

---

## Useful Display Filters for Defensive Analysis

```
# All HTTP GET requests
http.request.method == "GET"

# HTTP POST requests (may contain form data/credentials)
http.request.method == "POST"

# DNS queries
dns.flags.response == 0

# DNS responses
dns.flags.response == 1

# Large DNS responses (possible DNS tunneling)
dns && frame.len > 512

# All TCP SYN packets (new connection attempts)
tcp.flags.syn == 1 && tcp.flags.ack == 0

# TCP RST packets (connection resets)
tcp.flags.reset == 1

# Retransmissions (possible packet loss or evasion)
tcp.analysis.retransmission

# FTP credentials in cleartext
ftp.request.command == "PASS"

# ICMP ping requests
icmp.type == 8

# ARP requests (useful for detecting ARP scans)
arp.opcode == 1

# Detect cleartext passwords
http contains "password"
```

---

## TShark (Command-Line Wireshark)

`tshark` is the terminal version of Wireshark — useful for scripting and automation.

```bash
# Capture live traffic
sudo tshark -i eth0

# Apply a display filter
sudo tshark -i eth0 -Y "http"

# Read a pcap file
tshark -r capture.pcap

# Read with filter
tshark -r capture.pcap -Y "ip.addr == 192.168.1.1"

# Extract specific fields
tshark -r capture.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport

# Count packets by IP
tshark -r capture.pcap -T fields -e ip.src | sort | uniq -c | sort -rn

# Export HTTP objects (files downloaded)
tshark -r capture.pcap --export-objects http,./exported_files/
```

---

## Tips for Defensive Analysis

- Use **Statistics → Conversations** to see all host-to-host communication
- Use **Statistics → Protocol Hierarchy** to spot unusual protocol usage
- Use **Statistics → Endpoints** to find hosts sending unusually high traffic
- Use **Analyze → Follow TCP Stream** to reconstruct full sessions
- Use **File → Export Objects** to extract files from HTTP/SMB traffic
- Color rules help visually identify anomalies (Edit → Coloring Rules)

---

> 💡 Wireshark can open `.pcap` files produced by `tcpdump`, `tshark`, or any standard packet capture tool.
