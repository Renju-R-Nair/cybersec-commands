# 🔍 Nmap — Network Scanner

Nmap (Network Mapper) is the industry-standard tool for network discovery and security auditing.

---

## Basic Syntax

```bash
nmap [scan type] [options] <target>
```

---

## Host Discovery

```bash
# Ping scan — discover live hosts without port scanning
nmap -sn 192.168.1.0/24

# Scan a single host
nmap 192.168.1.1

# Scan a range of IPs
nmap 192.168.1.1-50

# Scan from a file list of hosts
nmap -iL targets.txt

# Treat all hosts as online (skip ping)
nmap -Pn 192.168.1.1
```

---

## Port Scanning

```bash
# Scan top 1000 ports (default)
nmap 192.168.1.1

# Scan all 65535 ports
nmap -p- 192.168.1.1

# Scan specific ports
nmap -p 22,80,443 192.168.1.1

# Scan a port range
nmap -p 1-1024 192.168.1.1

# Fast scan (top 100 ports)
nmap -F 192.168.1.1
```

---

## Scan Types

```bash
# TCP SYN scan (stealthy, requires root)
nmap -sS 192.168.1.1

# TCP Connect scan (no root needed)
nmap -sT 192.168.1.1

# UDP scan
nmap -sU 192.168.1.1

# TCP ACK scan (firewall rule mapping)
nmap -sA 192.168.1.1

# Null scan (no flags)
nmap -sN 192.168.1.1
```

---

## Service & Version Detection

```bash
# Detect service versions
nmap -sV 192.168.1.1

# Detect OS
nmap -O 192.168.1.1

# Aggressive scan (OS + version + scripts + traceroute)
nmap -A 192.168.1.1
```

---

## NSE Scripts (Nmap Scripting Engine)

```bash
# Run default scripts
nmap -sC 192.168.1.1

# Run a specific script
nmap --script=http-title 192.168.1.1

# Run scripts by category (safe, vuln, auth, etc.)
nmap --script=vuln 192.168.1.1

# Check for common vulnerabilities (defensive recon)
nmap --script=safe 192.168.1.1
```

---

## Output Options

```bash
# Normal output to file
nmap -oN output.txt 192.168.1.1

# XML output
nmap -oX output.xml 192.168.1.1

# Grepable output
nmap -oG output.gnmap 192.168.1.1

# All formats at once
nmap -oA output 192.168.1.1
```

---

## Timing & Performance

| Flag | Template | Description |
|------|----------|-------------|
| `-T0` | Paranoid | Very slow, IDS evasion |
| `-T1` | Sneaky | Slow |
| `-T2` | Polite | Less bandwidth |
| `-T3` | Normal | Default |
| `-T4` | Aggressive | Faster (reliable networks) |
| `-T5` | Insane | Very fast, may miss results |

---

## Useful Combinations

```bash
# Full defensive recon scan
nmap -sS -sV -sC -O -p- -T4 -oA full_scan 192.168.1.1

# Quick network sweep
nmap -sn -T4 192.168.1.0/24

# Detect open ports and running services on subnet
nmap -sV -p 22,80,443,3389,8080 192.168.1.0/24
```

---

> ⚠️ Always scan only networks and systems you are authorized to test.
