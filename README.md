<p align="center">
  <img src="./mrecon.png" width="280">
</p>

# WebRecon Methodology

A practical Web Recon methodology for authorized Bug Bounty and Web Application Security testing.

---

# 0. Scope & Target Preparation

Check:

- In-Scope Assets
- Out-of-Scope Assets
- Authentication Requirements
- Rate Limits
- Testing Restrictions
- Automation Restrictions

---

# 1. Asset Discovery

## 1.1 Passive Subdomain Enumeration

### Subfinder

```bash
subfinder -d TARGET -silent -o subfinder.txt
```

Output: `subfinder.txt`

### Amass

```bash
amass enum -passive -d TARGET -o amass.txt
```

Output: `amass.txt`

### Assetfinder

```bash
assetfinder TARGET > assetfinder.txt
```

Output: `assetfinder.txt`

### crt.sh

Certificate Transparency results → `crt.txt`

---

## 1.2 Merge & Deduplication

```bash
cat subfinder.txt amass.txt assetfinder.txt crt.txt > assets_raw.txt
```

```bash
sort -u assets_raw.txt > assets_clean.txt
```

Output: `assets_clean.txt`

---

# 2. DNS & Live Hosts

## 2.1 DNS Resolution

### dnsx

```bash
dnsx -l ../phase1/assets_final.txt -a -resp -o dnsx.txt
```

Output: `dnsx.txt`

---

## 2.2 HTTP Probing

### httpx

```bash
httpx -l ../phase1/assets_final.txt -silent -sc -title -tech-detect -ip -o httpx.txt
```

Output: `httpx.txt`

### Live Hosts

```bash
grep -E '^https?://' httpx.txt > live_hosts.txt
```

Output: `live_hosts.txt`

---

# 3. Technology & Infrastructure

## 3.1 Technology Detection

### WhatWeb

```bash
whatweb https://TARGET
```

```bash
whatweb -v https://TARGET
```

Output: `whatweb.txt`

---

## 3.2 Port Discovery

### Naabu

```bash
naabu -list ../phase2/live_hosts.txt -top-ports 100 -o naabu.txt
```

Single host:

```bash
naabu -host TARGET -top-ports 100
```

Output: `naabu.txt`

---

## 3.3 Service Enumeration

### Nmap

```bash
nmap -sV -sC -p PORTS TARGET
```

Output: `nmap.txt`

### SSH

```bash
nmap -p22 --script ssh2-enum-algos TARGET
```

Output: `service-enumeration/`

---

# 4. Web Discovery

## 4.1 Crawling

### Katana

```bash
katana -u https://TARGET -silent -o katana.txt
```

Multiple targets:

```bash
katana -list ../phase2/live_hosts.txt -silent -o katana.txt
```

JavaScript:

```bash
katana -list ../phase2/live_hosts.txt -silent -jc -o katana-js.txt
```

Outputs:

- `katana.txt`
- `katana-js.txt`

### GoSpider

```bash
gospider -s https://TARGET -o gospider
```

Output: `gospider/`

### Hakrawler

```bash
echo https://TARGET | hakrawler > hakrawler.txt
```

Output: `hakrawler.txt`

---

## 4.2 Historical URLs

### GAU

```bash
gau TARGET > gau.txt
```

### Waybackurls

```bash
waybackurls TARGET > wayback.txt
```

### Waymore

```bash
waymore -i TARGET -mode U
```

Outputs:

- `gau.txt`
- `wayback.txt`
- `waymore/`

---

## 4.3 Merge URLs

```bash
cat katana.txt gospider.txt hakrawler.txt gau.txt wayback.txt waymore.txt > urls_raw.txt
```

```bash
sort -u urls_raw.txt > urls_clean.txt
```

Output: `urls_clean.txt`

---

## 4.4 Content Discovery

### FFUF

```bash
ffuf -u https://TARGET/FUZZ -w WORDLIST
```

JSON:

```bash
ffuf -u https://TARGET/FUZZ -w WORDLIST -of json -o ffuf.json
```

### Feroxbuster

```bash
feroxbuster -u https://TARGET -w WORDLIST
```

### Dirsearch

```bash
dirsearch -u https://TARGET
```

### BFAC

Use selectively according to program scope and rules.

---

# 5. JavaScript Recon

## 5.1 JavaScript Collection

### SubJS

```bash
subjs -i phase4/urls_clean.txt -o js.txt
```

Output: `js.txt`

### Katana

```bash
katana -list phase2/live_hosts.txt -silent -jc -o katana-js.txt
```

---

## 5.2 JavaScript Analysis

### Mantra

Analyze for:

- Endpoints
- URLs
- Parameters
- API paths
- Configuration
- Interesting strings
- Potential secrets

### JSleak

Analyze for:

- API Keys
- Tokens
- Secrets
- Endpoints
- Configuration

### Manual Search

```bash
grep -Eio 'api|token|secret|key|password' file.js
```

```bash
grep -Eo 'src="[^"]+\.js[^"]*"' file.html
```

---

# 6. Attack Surface Mapping

Collect and correlate:

```text
Hosts
IPs
Ports
Services
Technologies
URLs
Endpoints
Parameters
APIs
Authentication
JavaScript
Interesting Assets
```

Use:

```text
templates/attack-surface.md
```

Output:

```text
phase6/attack-surface.md
```

---

# 7. Public Exposure Recon

## 7.1 GitHub

### GitDorker

Authorized GitHub reconnaissance.

### shhgit

Public GitHub content discovery.

### git-wild-hunt

Additional GitHub/public repository discovery.

---

## 7.2 Secret Detection

### TruffleHog

```bash
trufflehog github --org=TARGET_ORG
```

Output: `trufflehog/`

---

## 7.3 Public Documents

### MetaFinder

Look for:

- Metadata
- Usernames
- Internal Paths
- Software Information
- Organization Information

---

## 7.4 Public Web Intelligence

Sources:

- URLScan
- Common Crawl

Look for:

- Historical URLs
- Hosts
- Endpoints
- Infrastructure References

---

# 8. Analysis & Handoff

## 8.1 Correlation

Correlate:

```text
Assets
+
DNS
+
HTTP
+
Technologies
+
Ports
+
URLs
+
Endpoints
+
Parameters
+
JavaScript
+
Public Exposure
```

---

## 8.2 Testing Queue

Organize:

```text
Target
Surface
Endpoint
Method
Parameters
Authentication
Technology
Source
Notes
```

Example:

```text
Target: api.target.com
Surface: API
Endpoint: /api/user
Method: GET
Parameter: id
Authentication: Required
```

---

## 8.3 Final Report

Use:

```text
templates/recon-report.md
```

---

# Workflow

```text
Scope
  ↓
Asset Discovery
  ↓
DNS & Live Hosts
  ↓
Technology & Infrastructure
  ↓
Web Discovery
  ↓
JavaScript Recon
  ↓
Attack Surface Mapping
  ↓
Public Exposure
  ↓
Analysis & Handoff
  ↓
Manual Security Testing
```

---

# Repository Structure

```text
MyRecon/
│
├── docs/
├── methodology/
├── templates/
├── tools/
├── README.md
└── mrecon.png
```

> Authorized targets only.
