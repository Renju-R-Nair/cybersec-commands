# 🌐 httpx — HTTP Probe & Fingerprinting

httpx is a fast and multi-purpose HTTP toolkit by ProjectDiscovery. It probes URLs/hosts to check if they are alive, fingerprints web servers, and extracts useful information like status codes, titles, technologies, and more.

> GitHub: https://github.com/projectdiscovery/httpx

---

## Installation

```bash
# Using Go
go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest

# Using Homebrew (macOS/Linux)
brew install httpx

# Download binary (Windows)
# https://github.com/projectdiscovery/httpx/releases
# Extract httpx.exe and add to PATH

# Verify installation
httpx -version
```

---

## Basic Usage

```bash
# Probe a single URL
httpx -u https://example.com

# Probe a list of URLs/hosts from a file
httpx -l hosts.txt

# Pipe from another tool (e.g. subfinder)
subfinder -d example.com -silent | httpx

# Silent mode (only print live URLs)
httpx -l hosts.txt -silent
```

---

## Probing & Filtering

```bash
# Check both HTTP and HTTPS
httpx -l hosts.txt

# Only probe HTTPS
httpx -l hosts.txt -https-only

# Follow redirects
httpx -l hosts.txt -follow-redirects

# Filter by status code
httpx -l hosts.txt -mc 200
httpx -l hosts.txt -mc 200,301,302

# Exclude status codes
httpx -l hosts.txt -fc 404,403

# Filter by response size (bytes)
httpx -l hosts.txt -ms 1000

# Filter by response time (milliseconds)
httpx -l hosts.txt -mrt 500

# Match specific string in response body
httpx -l hosts.txt -match-string "admin"

# Match regex in response body
httpx -l hosts.txt -match-regex "version=[0-9]+"
```

---

## Fingerprinting & Information Extraction

```bash
# Show page title
httpx -l hosts.txt -title

# Show HTTP status code
httpx -l hosts.txt -status-code

# Show web server (Server header)
httpx -l hosts.txt -web-server

# Show content type
httpx -l hosts.txt -content-type

# Show response size
httpx -l hosts.txt -content-length

# Show IP address
httpx -l hosts.txt -ip

# Show response time
httpx -l hosts.txt -response-time

# Show all response headers
httpx -l hosts.txt -response-header

# Detect technologies (Wappalyzer-based)
httpx -l hosts.txt -tech-detect

# Extract page title, status, tech, server in one command
httpx -l hosts.txt -title -status-code -tech-detect -web-server -ip
```

---

## Security-Focused Checks

```bash
# Check for security headers
httpx -l hosts.txt -response-header | grep -i "strict-transport\|x-frame\|content-security"

# Detect HTTPS vs HTTP
httpx -l hosts.txt -status-code -follow-redirects

# Check for open redirects
httpx -l hosts.txt -follow-redirects -match-string "attacker.com"

# Extract all URLs from response body
httpx -l hosts.txt -extract-regex "https?://[a-zA-Z0-9./?=_%:-]*"

# Probe for admin panels
httpx -l hosts.txt -path /admin -status-code -title

# Check multiple paths at once
httpx -l hosts.txt -path /admin,/login,/dashboard,/wp-admin -status-code -title

# Detect virtual hosts
httpx -l hosts.txt -vhost
```

---

## Output Options

```bash
# Save output to file
httpx -l hosts.txt -o results.txt

# JSON output (best for automation)
httpx -l hosts.txt -json -o results.json

# CSV output
httpx -l hosts.txt -csv -o results.csv

# Store response body to a folder
httpx -l hosts.txt -store-response -store-response-dir ./responses/

# Show full response chain
httpx -l hosts.txt -include-chain
```

---

## Rate Limiting & Performance

```bash
# Set number of concurrent threads
httpx -l hosts.txt -threads 50

# Set rate limit (requests per second)
httpx -l hosts.txt -rate-limit 100

# Set timeout (seconds)
httpx -l hosts.txt -timeout 10

# Retries on failure
httpx -l hosts.txt -retries 2

# Random user agent
httpx -l hosts.txt -random-agent
```

---

## Combining With Other Tools

httpx is most powerful in recon pipelines.

```bash
# subfinder → httpx (find live subdomains)
subfinder -d example.com -silent | httpx -silent

# subfinder → httpx → nuclei (full recon + vuln scan)
subfinder -d example.com -silent | httpx -silent | nuclei -tags cve,misconfig

# subfinder → httpx with full fingerprint
subfinder -d example.com -silent | httpx -title -status-code -tech-detect -web-server -ip -o recon.txt

# cat a list of IPs → httpx (probe internal assets)
cat ip_ranges.txt | httpx -status-code -title -silent

# Probe for exposed login pages
subfinder -d example.com -silent | httpx -path /login -status-code -title -mc 200
```

---

## Defensive Use Cases

```bash
# Map all live assets on your domain
subfinder -d yourcompany.com -silent | httpx -title -status-code -tech-detect -o live_assets.txt

# Detect exposed admin panels
subfinder -d yourcompany.com -silent | httpx -path /admin,/wp-admin,/phpmyadmin -mc 200 -title

# Find assets missing HTTPS
subfinder -d yourcompany.com -silent | httpx -status-code | grep "http://"

# Detect outdated/vulnerable technologies
subfinder -d yourcompany.com -silent | httpx -tech-detect -json -o tech.json

# Check security headers across all assets
subfinder -d yourcompany.com -silent | httpx -response-header -o headers.txt
grep -v "Strict-Transport-Security" headers.txt   # missing HSTS
grep -v "X-Frame-Options" headers.txt             # missing clickjacking protection
```

---

## Useful Flags Summary

| Flag | Description |
|------|-------------|
| `-u` | Single target URL |
| `-l` | File with list of targets |
| `-silent` | Print only results |
| `-title` | Extract page title |
| `-status-code` | Show HTTP status code |
| `-tech-detect` | Detect technologies |
| `-web-server` | Show web server header |
| `-ip` | Show resolved IP |
| `-content-length` | Show response size |
| `-response-time` | Show response time |
| `-follow-redirects` | Follow HTTP redirects |
| `-mc` | Match specific status codes |
| `-fc` | Filter out status codes |
| `-path` | Probe a specific path |
| `-threads` | Concurrent threads |
| `-rate-limit` | Requests per second |
| `-o` | Output file |
| `-json` | JSON output |
| `-random-agent` | Use random User-Agent |

---

## Tips

- Always use `-silent` when piping httpx into other tools
- Use `-tech-detect` to quickly identify what tech stack a target is running
- Use `-random-agent` to avoid simple WAF/bot detection
- Combine `-path` with a list of common sensitive paths for quick recon
- JSON output (`-json`) is best when you need to parse results programmatically

---

> ⚠️ Only probe hosts and domains you own or have explicit written permission to test. Unauthorized probing may be illegal.
