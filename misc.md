# 🧰 Miscellaneous Security Tools & Commands

A collection of essential commands and tools used in day-to-day defensive security work.

---

## dig — DNS Lookup & Investigation

```bash
# Basic lookup
dig example.com

# Query a specific record type
dig example.com A
dig example.com MX
dig example.com TXT
dig example.com NS

# Reverse DNS lookup
dig -x 8.8.8.8

# Query a specific DNS server
dig @8.8.8.8 example.com

# Short output
dig +short example.com

# Trace DNS delegation
dig +trace example.com

# Check for DNS zone transfer (misconfiguration test)
dig axfr @ns1.example.com example.com
```

---

## curl — HTTP Inspection & Testing

```bash
# Basic request
curl https://example.com

# Show response headers only
curl -I https://example.com

# Show request + response headers
curl -v https://example.com

# Follow redirects
curl -L https://example.com

# Custom user agent
curl -A "Mozilla/5.0" https://example.com

# POST data
curl -X POST -d "param=value" https://example.com/api

# Save output to file
curl -o output.html https://example.com

# Test with a specific IP (bypass DNS)
curl --resolve example.com:443:93.184.216.34 https://example.com

# Check SSL certificate details
curl -vI https://example.com 2>&1 | grep -A 20 "Server certificate"

# Test for open redirect
curl -v "https://example.com/redirect?url=https://attacker.com"
```

---

## openssl — Certificate & Crypto Inspection

```bash
# Check a remote SSL/TLS certificate
openssl s_client -connect example.com:443

# Show certificate details
echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -text -noout

# Check certificate expiry
echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -dates

# Test TLS version support
openssl s_client -connect example.com:443 -tls1_2
openssl s_client -connect example.com:443 -tls1_3

# Generate a hash of a file (integrity check)
openssl dgst -sha256 file.bin
```

---

## whois — Domain & IP Ownership

```bash
# Domain WHOIS
whois example.com

# IP WHOIS
whois 8.8.8.8

# Find registrar and registration dates
whois example.com | grep -E "Registrar|Creation|Expiry"
```

---

## ss — Socket Statistics (see netstat.md for full reference)

```bash
# Quick listening ports
ss -tlnp

# All established connections
ss -tnp state established
```

---

## lsof — List Open Files / Connections

```bash
# Show all network connections
sudo lsof -i

# Connections on a specific port
sudo lsof -i :443

# Connections by a specific process
sudo lsof -i -p <PID>

# Files opened by a specific user
sudo lsof -u username

# What process is listening on a port
sudo lsof -iTCP:22 -sTCP:LISTEN
```

---

## ps & top — Process Monitoring

```bash
# Show all running processes
ps aux

# Filter by process name
ps aux | grep nginx

# Show process tree
ps axjf

# Real-time process monitor
top

# Better process monitor (install if needed)
htop

# Find process by port (combine with ss)
ss -tlnp | grep ':80'
```

---

## find — Locate Suspicious Files

```bash
# Find SUID/SGID binaries (privilege escalation targets)
find / -perm /4000 -type f 2>/dev/null
find / -perm /2000 -type f 2>/dev/null

# Find recently modified files (last 24 hours)
find /var /tmp /etc -mtime -1 -type f 2>/dev/null

# Find world-writable files
find / -perm -o+w -type f 2>/dev/null

# Find files owned by root with execute permission
find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null
```

---

## hash — File Integrity

```bash
# MD5 hash
md5sum file.bin

# SHA256 hash (preferred)
sha256sum file.bin

# Compare hash to known good
sha256sum file.bin | grep "expectedhashvalue"
```

---

## journalctl & log analysis

```bash
# View system logs
sudo journalctl -xe

# View logs for a specific service
sudo journalctl -u ssh

# Follow logs live
sudo journalctl -f

# Filter by time
sudo journalctl --since "2024-01-01" --until "2024-01-02"

# View auth logs (login attempts)
sudo cat /var/log/auth.log | grep "Failed password"

# Count failed SSH attempts by IP
sudo grep "Failed password" /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn
```

---

> 💡 These tools are most powerful when combined. Example: capture with `tcpdump`, analyze with `Wireshark`, and correlate with `journalctl` logs for full incident context.
