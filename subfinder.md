# 🔎 Subfinder — Subdomain Discovery Tool

Subfinder is a fast passive subdomain discovery tool by ProjectDiscovery. It uses multiple passive sources (APIs, certificate transparency logs, DNS datasets) to enumerate subdomains without directly interacting with the target.

> GitHub: https://github.com/projectdiscovery/subfinder

---

## Installation

```bash
# Using Go
go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest

# Using Homebrew (macOS/Linux)
brew install subfinder

# Download binary (Windows)
# https://github.com/projectdiscovery/subfinder/releases
# Extract subfinder.exe and add to PATH

# Verify installation
subfinder -version
```

---

## Basic Usage

```bash
# Enumerate subdomains for a domain
subfinder -d example.com

# Silent mode (only print subdomains, no banner)
subfinder -d example.com -silent

# Save output to a file
subfinder -d example.com -o subdomains.txt

# Scan multiple domains from a file
subfinder -dL domains.txt -o subdomains.txt
```

---

## Output Options

```bash
# Plain text output (default)
subfinder -d example.com -o output.txt

# JSON output
subfinder -d example.com -json -o output.json

# Output with source (which API found each subdomain)
subfinder -d example.com -sources

# Output IP addresses along with subdomains
subfinder -d example.com -oI

# Only output unique results
subfinder -d example.com -silent | sort -u
```

---

## Sources & API Keys

Subfinder pulls from many passive sources. Some require free API keys for better results.

```bash
# List all available sources
subfinder -ls

# Use all sources (including slow ones)
subfinder -d example.com -all

# Use specific sources only
subfinder -d example.com -sources shodan,censys,virustotal

# Exclude specific sources
subfinder -d example.com -exclude-sources shodan
```

### Setting Up API Keys

Edit the config file at:
- **Linux/macOS:** `~/.config/subfinder/provider-config.yaml`
- **Windows:** `%USERPROFILE%\.config\subfinder\provider-config.yaml`

Example config:
```yaml
shodan:
  - YOUR_SHODAN_API_KEY
censys:
  - YOUR_CENSYS_API_ID:YOUR_CENSYS_SECRET
virustotal:
  - YOUR_VT_API_KEY
chaos:
  - YOUR_CHAOS_API_KEY
securitytrails:
  - YOUR_ST_API_KEY
```

> Free API keys available at: Shodan, VirusTotal, SecurityTrails, Censys, Chaos (ProjectDiscovery)

---

## Rate Limiting & Performance

```bash
# Set number of concurrent goroutines
subfinder -d example.com -t 50

# Set timeout (seconds)
subfinder -d example.com -timeout 30

# Max time to wait for results (seconds)
subfinder -d example.com -max-time 10
```

---

## Recursive Subdomain Enumeration

```bash
# Enumerate subdomains, then enumerate subdomains of subdomains
subfinder -d example.com -recursive -silent
```

---

## Combining With Other Tools

Subfinder is most powerful when piped into other tools.

```bash
# Pipe into httpx to find live hosts
subfinder -d example.com -silent | httpx -silent

# Pipe into httpx then nuclei for vulnerability scanning
subfinder -d example.com -silent | httpx -silent | nuclei -tags misconfig,exposure

# Pipe into nmap for port scanning
subfinder -d example.com -silent | nmap -iL - -p 80,443

# Pipe into dnsx for DNS resolution
subfinder -d example.com -silent | dnsx -silent

# Full recon pipeline
subfinder -d example.com -silent | \
  httpx -silent -o live_hosts.txt && \
  nuclei -l live_hosts.txt -severity critical,high -o vulns.txt
```

---

## Defensive Use Cases

```bash
# Map your own attack surface (find all your subdomains)
subfinder -d yourcompany.com -silent -o all_subdomains.txt

# Find forgotten/shadow IT subdomains
subfinder -d yourcompany.com -all -silent | sort -u

# Monitor for new subdomains (run periodically and diff)
subfinder -d yourcompany.com -silent -o new_scan.txt
diff previous_scan.txt new_scan.txt

# Find subdomains and check which are live
subfinder -d yourcompany.com -silent | httpx -silent -o live.txt

# Full passive recon on your domain
subfinder -d yourcompany.com -all -sources -o recon_with_sources.txt
```

---

## Useful Flags Summary

| Flag | Description |
|------|-------------|
| `-d` | Target domain |
| `-dL` | File with list of domains |
| `-o` | Output file |
| `-silent` | Print only results |
| `-json` | JSON output |
| `-all` | Use all sources |
| `-sources` | Show which source found each result |
| `-oI` | Include IP addresses |
| `-t` | Number of concurrent threads |
| `-timeout` | Timeout per source (seconds) |
| `-recursive` | Recursive subdomain enumeration |
| `-ls` | List all available sources |
| `-version` | Show version |

---

## Recommended Free API Keys to Set Up

| Service | URL | Why |
|---------|-----|-----|
| VirusTotal | virustotal.com | Large subdomain dataset |
| SecurityTrails | securitytrails.com | Historical DNS data |
| Shodan | shodan.io | Internet-wide scan data |
| Censys | censys.io | Certificate transparency |
| Chaos | chaos.projectdiscovery.io | Bug bounty program data |
| BufferOver | Free, no key needed | DNS datasets |

---

## Tips

- Always use `-silent` when piping into other tools to avoid extra output
- Use `-all` for maximum coverage on important targets
- Set up API keys — they dramatically increase the number of subdomains found
- Run subfinder periodically on your own assets to catch new/forgotten subdomains
- Combine with `httpx` before scanning — many subdomains won't be live

---

> ⚠️ Subfinder is a **passive** tool — it never directly contacts the target domain. All results come from third-party sources. Always ensure you have authorization before acting on discovered subdomains.
