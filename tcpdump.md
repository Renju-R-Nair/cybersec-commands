# 📦 Tcpdump — Packet Capture & Analysis

`tcpdump` is a powerful command-line packet analyzer used for capturing and inspecting network traffic in real time.

---

## Basic Syntax

```bash
tcpdump [options] [filter expression]
```

> Requires root/sudo on most systems.

---

## Basic Captures

```bash
# Capture on default interface
sudo tcpdump

# Capture on a specific interface
sudo tcpdump -i eth0

# List available interfaces
sudo tcpdump -D

# Capture on all interfaces
sudo tcpdump -i any

# Limit number of packets
sudo tcpdump -c 100

# Disable DNS resolution (faster, shows raw IPs)
sudo tcpdump -n

# Show packet contents in hex and ASCII
sudo tcpdump -XX
```

---

## Saving & Reading Captures

```bash
# Save capture to a .pcap file
sudo tcpdump -w capture.pcap

# Save with timestamp in filename
sudo tcpdump -w capture_$(date +%F_%T).pcap

# Read from a saved file
sudo tcpdump -r capture.pcap

# Read and filter from file
sudo tcpdump -r capture.pcap 'tcp port 80'
```

---

## Filtering Traffic (BPF Syntax)

### By Host

```bash
# Capture traffic to/from a host
sudo tcpdump host 192.168.1.1

# Capture only from source
sudo tcpdump src host 192.168.1.1

# Capture only to destination
sudo tcpdump dst host 192.168.1.1
```

### By Port

```bash
# Capture traffic on a specific port
sudo tcpdump port 443

# Capture traffic on source port
sudo tcpdump src port 80

# Capture traffic on destination port
sudo tcpdump dst port 22
```

### By Protocol

```bash
# TCP only
sudo tcpdump tcp

# UDP only
sudo tcpdump udp

# ICMP only (useful for ping detection)
sudo tcpdump icmp

# ARP traffic
sudo tcpdump arp
```

### By Network / Subnet

```bash
# Capture all traffic on a subnet
sudo tcpdump net 192.168.1.0/24

# Traffic from a subnet
sudo tcpdump src net 10.0.0.0/8
```

### Combining Filters

```bash
# AND
sudo tcpdump host 192.168.1.1 and port 443

# OR
sudo tcpdump port 80 or port 443

# NOT
sudo tcpdump not port 22

# Complex example: HTTP traffic not from localhost
sudo tcpdump -i eth0 'tcp port 80 and not src host 127.0.0.1'
```

---

## Defensive Use Cases

```bash
# Monitor all inbound SSH connections
sudo tcpdump -i eth0 'tcp dst port 22'

# Detect ICMP flood (ping flood)
sudo tcpdump -i eth0 icmp

# Capture DNS queries (detect DNS tunneling)
sudo tcpdump -i eth0 -n 'udp port 53'

# Detect ARP spoofing
sudo tcpdump -i eth0 arp

# Monitor traffic to/from suspicious IP
sudo tcpdump -i eth0 host 203.0.113.5

# Capture HTTP headers only (first 200 bytes)
sudo tcpdump -i eth0 -A -s 200 'tcp port 80'

# Full packet capture for forensics
sudo tcpdump -i eth0 -s 0 -w forensics.pcap
```

---

## Output Verbosity

```bash
# Verbose output
sudo tcpdump -v

# More verbose
sudo tcpdump -vv

# Even more verbose
sudo tcpdump -vvv
```

---

## Rotating Capture Files

```bash
# Rotate every 100MB
sudo tcpdump -w capture_%Y%m%d_%H%M%S.pcap -G 0 -C 100

# Rotate every 60 seconds
sudo tcpdump -w capture_%Y%m%d_%H%M%S.pcap -G 60
```

---

> 💡 Tip: Use Wireshark to open `.pcap` files for detailed GUI-based analysis.
> 
> ⚠️ Always capture only on networks you are authorized to monitor.
