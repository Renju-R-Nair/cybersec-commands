# 🔥 Firewall Management

---

## UFW (Uncomplicated Firewall) — Ubuntu/Debian

UFW is the default firewall tool on Ubuntu and provides a simple interface over `iptables`.

```bash
# Enable / Disable
sudo ufw enable
sudo ufw disable

# Check status and rules
sudo ufw status
sudo ufw status verbose
sudo ufw status numbered

# Allow a port
sudo ufw allow 22
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Deny a port
sudo ufw deny 23         # Block Telnet
sudo ufw deny 3389/tcp   # Block RDP

# Allow from a specific IP
sudo ufw allow from 192.168.1.100
sudo ufw allow from 192.168.1.0/24 to any port 22

# Delete a rule
sudo ufw delete allow 80
sudo ufw delete 3          # Delete rule #3 (from numbered list)

# Default policies (hardening baseline)
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Reset all rules
sudo ufw reset

# Reload rules
sudo ufw reload

# Logging
sudo ufw logging on
sudo ufw logging medium    # low | medium | high | full
```

---

## iptables — Linux Firewall (Advanced)

```bash
# View all rules
sudo iptables -L -v -n
sudo iptables -L -v -n --line-numbers

# Allow established/related connections (stateful firewall)
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Allow loopback
sudo iptables -A INPUT -i lo -j ACCEPT

# Allow SSH
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow HTTP/HTTPS
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Drop all other inbound traffic (default deny)
sudo iptables -A INPUT -j DROP

# Block a specific IP
sudo iptables -A INPUT -s 203.0.113.5 -j DROP

# Block a subnet
sudo iptables -A INPUT -s 203.0.113.0/24 -j DROP

# Rate-limit SSH (brute force protection)
sudo iptables -A INPUT -p tcp --dport 22 -m limit --limit 3/min --limit-burst 5 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j DROP

# Log dropped packets
sudo iptables -A INPUT -j LOG --log-prefix "DROPPED: " --log-level 4

# Delete a rule by line number
sudo iptables -D INPUT 3

# Flush all rules
sudo iptables -F

# Save rules (Debian/Ubuntu)
sudo iptables-save > /etc/iptables/rules.v4

# Restore rules
sudo iptables-restore < /etc/iptables/rules.v4
```

---

## firewalld — CentOS/RHEL/Fedora

```bash
# Start / Enable
sudo systemctl start firewalld
sudo systemctl enable firewalld

# Check status
sudo firewall-cmd --state
sudo firewall-cmd --list-all

# Allow a service
sudo firewall-cmd --permanent --add-service=ssh
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https

# Allow a port
sudo firewall-cmd --permanent --add-port=8080/tcp

# Remove a service/port
sudo firewall-cmd --permanent --remove-service=telnet
sudo firewall-cmd --permanent --remove-port=8080/tcp

# Block an IP
sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="203.0.113.5" drop'

# Reload to apply changes
sudo firewall-cmd --reload

# List open ports
sudo firewall-cmd --list-ports
```

---

## Windows Firewall (PowerShell)

```powershell
# View all firewall rules
Get-NetFirewallRule | Select-Object DisplayName, Enabled, Direction, Action

# Block inbound on a port
New-NetFirewallRule -DisplayName "Block Port 23" -Direction Inbound -Protocol TCP -LocalPort 23 -Action Block

# Allow inbound on a port
New-NetFirewallRule -DisplayName "Allow HTTPS" -Direction Inbound -Protocol TCP -LocalPort 443 -Action Allow

# Block an IP address
New-NetFirewallRule -DisplayName "Block IP" -Direction Inbound -RemoteAddress 203.0.113.5 -Action Block

# Enable/Disable a rule
Set-NetFirewallRule -DisplayName "Block Port 23" -Enabled True
Set-NetFirewallRule -DisplayName "Block Port 23" -Enabled False

# Remove a rule
Remove-NetFirewallRule -DisplayName "Block Port 23"
```

---

> 💡 **Hardening Tip:** Default-deny all inbound traffic and explicitly allow only what's needed. Log dropped packets for visibility.
