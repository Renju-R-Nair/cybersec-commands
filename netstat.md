# 📡 Netstat & SS — Network Connection Monitoring

`netstat` is a classic tool for displaying network connections, routing tables, and interface statistics. On modern Linux systems, `ss` is the faster, recommended replacement.

---

## Netstat

### Basic Usage

```bash
# All connections (TCP + UDP)
netstat -a

# Show only TCP connections
netstat -at

# Show only UDP connections
netstat -au

# Show listening ports only
netstat -l

# Show listening TCP ports
netstat -lt

# Show numeric addresses (skip DNS resolution)
netstat -n

# Show PID and program name
netstat -p
```

### Most Useful Combinations

```bash
# All listening ports with PID and numeric addresses
netstat -tlnp

# All active TCP connections with PID
netstat -tnp

# All UDP listening ports
netstat -ulnp

# Show network statistics
netstat -s

# Show routing table
netstat -r

# Show interface statistics
netstat -i

# Continuous monitoring (refresh every second)
netstat -c
```

### Defensive Use Cases

```bash
# Find suspicious outbound connections
netstat -tnp | grep ESTABLISHED

# Look for unknown listening services
netstat -tlnp

# Detect unexpected foreign addresses
netstat -tn | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -rn
```

---

## SS (Socket Statistics) — Modern Replacement

`ss` is faster and more feature-rich than `netstat`.

### Basic Usage

```bash
# All connections
ss -a

# TCP connections
ss -t

# UDP connections
ss -u

# Listening sockets
ss -l

# Show process info
ss -p

# Numeric output
ss -n
```

### Most Useful Combinations

```bash
# All listening TCP ports with process names
ss -tlnp

# All established connections
ss -tnp state established

# All connections to a specific port
ss -tn dport = :443

# Connections from a specific source IP
ss -tn src 192.168.1.100

# Show socket memory usage
ss -tm
```

### State Filtering

```bash
# Connections in a specific state
ss -t state established
ss -t state time-wait
ss -t state close-wait
ss -t state listen

# Exclude a state
ss -t exclude time-wait
```

---

## Comparison: netstat vs ss

| Feature         | `netstat`       | `ss`             |
|-----------------|-----------------|------------------|
| Speed           | Slower          | Much faster      |
| Kernel support  | /proc/net       | Netlink socket   |
| Default in      | Legacy systems  | Modern Linux     |
| More details    | Limited         | Extended stats   |

---

## Windows Equivalent

```powershell
# All connections with PIDs
netstat -ano

# Find which process owns a port
netstat -ano | findstr :443

# Map PID to process name
tasklist | findstr <PID>
```

---

> 💡 Tip: Combine with `grep`, `awk`, and `watch` for continuous monitoring.
> ```bash
> watch -n 2 'ss -tnp state established'
> ```
