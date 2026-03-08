# ⚡ Nuclei — Fast Vulnerability Scanner

Nuclei is a fast, template-based vulnerability scanner by ProjectDiscovery. It sends requests to targets based on pre-built templates covering CVEs, misconfigurations, exposed panels, and more.

> GitHub: https://github.com/projectdiscovery/nuclei

---

## Installation

```bash
# Using Go
go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest

# Using Homebrew (macOS/Linux)
brew install nuclei

# Download binary (Windows)
# https://github.com/projectdiscovery/nuclei/releases
# Extract and add to PATH

# Update Nuclei templates
nuclei -update-templates
```

---

## Basic Usage

```bash
# Scan a single target
nuclei -u https://example.com

# Scan a list of targets from a file
nuclei -l targets.txt

# Update templates before scanning
nuclei -update-templates && nuclei -u https://example.com
```

---

## Template-Based Scanning

```bash
# Run all default templates
nuclei -u https://example.com

# Run a specific template file
nuclei -u https://example.com -t templates/cves/2021/CVE-2021-44228.yaml

# Run all templates in a folder
nuclei -u https://example.com -t templates/cves/

# Run templates by tag
nuclei -u https://example.com -tags cve
nuclei -u https://example.com -tags sqli
nuclei -u https://example.com -tags xss
nuclei -u https://example.com -tags rce
nuclei -u https://example.com -tags lfi
nuclei -u https://example.com -tags misconfig
nuclei -u https://example.com -tags exposure

# Run multiple tags at once
nuclei -u https://example.com -tags cve,misconfig,exposure

# Exclude a tag
nuclei -u https://example.com -etags intrusive
```

---

## Severity Filtering

```bash
# Scan only for critical and high severity issues
nuclei -u https://example.com -severity critical,high

# Scan for all severities
nuclei -u https://example.com -severity critical,high,medium,low,info

# Exclude a severity
nuclei -u https://example.com -exclude-severity info
```

---

## Output Options

```bash
# Save output to a file
nuclei -u https://example.com -o results.txt

# JSON output (good for parsing/automation)
nuclei -u https://example.com -json -o results.json

# JSONL output (one JSON object per line)
nuclei -u https://example.com -jsonl -o results.jsonl

# Silent mode (only show findings)
nuclei -u https://example.com -silent

# Verbose mode (show all requests)
nuclei -u https://example.com -v
```

---

## Rate Limiting & Performance

```bash
# Set number of concurrent requests
nuclei -u https://example.com -c 50

# Set rate limit (requests per second)
nuclei -u https://example.com -rate-limit 100

# Set timeout per request (seconds)
nuclei -u https://example.com -timeout 10

# Retry failed requests
nuclei -u https://example.com -retries 3

# Bulk scanning with controlled rate
nuclei -l targets.txt -c 30 -rate-limit 150 -timeout 10
```

---

## Defensive / Recon Use Cases

```bash
# Check for exposed admin panels
nuclei -u https://example.com -tags panel

# Check for exposed sensitive files
nuclei -u https://example.com -tags exposure

# Check for security misconfigurations
nuclei -u https://example.com -tags misconfig

# Scan for known CVEs only
nuclei -u https://example.com -tags cve -severity critical,high

# Detect default credentials on login pages
nuclei -u https://example.com -tags default-login

# Scan for outdated software versions
nuclei -u https://example.com -tags version

# Check security headers
nuclei -u https://example.com -t templates/miscellaneous/security-headers.yaml

# Full defensive recon scan
nuclei -u https://example.com \
  -tags misconfig,exposure,panel,default-login \
  -severity critical,high,medium \
  -o recon_results.txt
```

---

## Scanning Multiple Targets

```bash
# From a file
nuclei -l urls.txt -tags cve -severity critical,high -o results.txt

# Pipe from another tool (e.g. subfinder)
subfinder -d example.com -silent | nuclei -tags misconfig

# Pipe from httpx (confirm live hosts first)
subfinder -d example.com | httpx -silent | nuclei -tags cve,misconfig
```

---

## Template Management

```bash
# Update all templates
nuclei -update-templates

# List all available templates
nuclei -tl

# Search templates by keyword
nuclei -tl | grep "apache"
nuclei -tl | grep "CVE-2021"

# Show template stats
nuclei -ts
```

---

## Useful Flags Summary

| Flag | Description |
|------|-------------|
| `-u` | Single target URL |
| `-l` | File with list of targets |
| `-t` | Specific template or folder |
| `-tags` | Filter by template tag |
| `-etags` | Exclude tags |
| `-severity` | Filter by severity |
| `-o` | Output file |
| `-json` | JSON output |
| `-silent` | Only print findings |
| `-c` | Concurrency (default: 25) |
| `-rate-limit` | Max requests per second |
| `-update-templates` | Pull latest templates |
| `-v` | Verbose mode |

---

## Tips for Defensive Security

- Always run `nuclei -update-templates` before scanning to get the latest CVE templates
- Use `-tags exposure` to find accidentally exposed files, configs, or secrets
- Combine with `subfinder` + `httpx` for full subdomain recon pipelines
- Use `-severity critical,high` for quick triage on large target lists
- Save all results with `-o` and review them — even `info` findings can reveal useful intel

---

> ⚠️ Only scan targets you own or have explicit written permission to test. Unauthorized scanning is illegal.
