<p align="center">
  <img src="./mrecon.png" width="300" alt="WebRecon Methodology">
</p>

<h1 align="center">WebRecon Methodology</h1>

<p align="center">
  A practical Web Reconnaissance methodology for Bug Bounty Hunting and Web Application Security.
</p>

<p align="center">
  Discover → Analyze → Correlate → Prioritize → Test
</p>

---

# About

**WebRecon Methodology** is a structured reconnaissance workflow designed for authorized Bug Bounty programs, Web Application Security assessments, and security research.

The methodology focuses on building a complete and organized view of the target before starting vulnerability testing.

The workflow does not depend on a single tool.

Instead, important tools are executed separately, their results are preserved, then the data is:

```text
Collect
   ↓
Normalize
   ↓
Merge
   ↓
Deduplicate
   ↓
Compare
   ↓
Analyze
   ↓
Correlate
   ↓
Prioritize
   ↓
Manual Security Testing
```

A tool finding is intelligence, not automatically a vulnerability.
---

# Methodology

```text
Web Recon Methodology
│
├── 0. Scope & Target Preparation
│
├── 1. Asset Discovery
│   ├── 1.1 Passive Subdomain Enumeration
│   ├── 1.2 Certificate Transparency
│   └── 1.3 Results Combination & Deduplication
│
├── 2. DNS & Live Host Discovery
│   ├── 2.1 DNS Resolution
│   ├── 2.2 HTTP/HTTPS Probing
│   └── 2.3 Status Code Analysis
│
├── 3. Technology & Infrastructure
│   ├── 3.1 Technology Detection
│   ├── 3.2 Port Discovery
│   ├── 3.3 Service Enumeration
│   └── 3.4 Infrastructure Mapping
│
├── 4. Web Discovery
│   ├── 4.1 Web Crawling
│   ├── 4.2 Historical URL Discovery
│   ├── 4.3 Endpoint Discovery
│   ├── 4.4 Content Discovery
│   └── 4.5 Results Combination
│
├── 5. JavaScript Recon
│   ├── 5.1 JavaScript Collection
│   ├── 5.2 JavaScript Analysis
│   ├── 5.3 Endpoint Extraction
│   └── 5.4 Secrets & Interesting Data
│
├── 6. Attack Surface Mapping
│   ├── 6.1 Host Inventory
│   ├── 6.2 Endpoint Inventory
│   ├── 6.3 Parameter Mapping
│   ├── 6.4 API Surface
│   └── 6.5 Authentication Surface
│
├── 7. Public Exposure Recon
│   ├── 7.1 GitHub Recon
│   ├── 7.2 Secret Detection
│   └── 7.3 Public Documents & Metadata
│
└── 8. Recon Analysis & Handoff
    ├── 8.1 Correlation
    ├── 8.2 Validation
    ├── 8.3 Prioritization
    └── 8.4 Testing Queue
```
---

# 0. Scope & Target Preparation

Before running any reconnaissance tool, understand exactly what is authorized.

## Objectives

- Identify the Bug Bounty program.
- Read the program policy.
- Define in-scope assets.
- Define out-of-scope assets.
- Identify authentication requirements.
- Identify rate limits.
- Identify automation restrictions.
- Identify prohibited techniques.
- Record important program notes.

## Target Information
```text
Program:
Target:
In-Scope Assets:
Out-of-Scope Assets:
Authentication:
Rate Limits:
Automation Restrictions:
Important Notes:
```
## Scope Principle

Never assume that an asset is in scope simply because it belongs to the same organization.
```text
Discovered Asset
      ↓
Check Program Scope
      ↓
In Scope?
   /       \
 Yes       No
  ↓         ↓
Continue   Stop
```
## Exit Criteria

Recon starts only when the following are clear:

- What can be tested?
- What cannot be tested?
- Which assets are in scope?
- Which techniques are restricted?
- Are authenticated tests allowed?
- Are automated tools allowed?

[Detailed Phase →](./methodology/00-scope.md)

---

# 1. Asset Discovery

The objective of this phase is to discover candidate assets from multiple passive sources.

## 1.1 Passive Subdomain Enumeration

🛠️ **Tools**: Subfinder, Amass, Assetfinder

### Subfinder

```bash
subfinder -d TARGET -silent -o subfinder.txt
```
Purpose:

- Passive subdomain discovery.
- Collect candidate subdomains from available sources.

Output:
```text
subfinder.txt
```
---

### Amass — Passive Mode
```bash
amass enum -passive -d TARGET -o amass.txt
```
Purpose:

- Additional passive asset discovery.
- Increase source diversity.

Output:
```text
amass.txt
```
---

### Assetfinder
```text
assetfinder TARGET > assetfinder.txt
```
Purpose:

- Discover additional candidate subdomains and related assets.

Output:
```text
assetfinder.txt
```
---

## 1.2 Certificate Transparency

🛠️ **Source:** crt.sh

Certificate Transparency logs can reveal hostnames that may not appear in other passive sources.

Save the results separately:
```text
crt.txt
```
CT results are treated as **candidate assets**.

They are not automatically:

- In scope.
- Alive.
- Vulnerable.

They must be validated in later phases.

---

## 1.3 Results Combination & Deduplication

Keep every source separately before merging.

### Merge

```bash
cat subfinder.txt amass.txt assetfinder.txt crt.txt > assets_raw.txt
```
### Normalize
```bash
sed 's/\r$//' assets_raw.txt | sed 's/^\*\.//' | sed '/^$/d' > assets_normalized.txt
```
### Deduplicate
```bash
sort -u assets_normalized.txt > assets_clean.txt
```
### Source Comparison

Keep individual source files available for comparison.
```bash
sort -u subfinder.txt > subfinder_sorted.txt
sort -u amass.txt > amass_sorted.txt
```
Example:
```bash
comm -12 subfinder_sorted.txt amass_sorted.txt
```
## Final Output
```text
subfinder.txt
amass.txt
assetfinder.txt
crt.txt

assets_raw.txt
assets_normalized.txt
assets_clean.txt
```
### Phase 1 Flow
```text
Authorized Target
      ↓
Subfinder
      ↓
Amass
      ↓
Assetfinder
      ↓
crt.sh
      ↓
Merge
      ↓
Normalize
      ↓
Deduplicate
      ↓
Compare Sources
      ↓
Final Asset Inventory
```
[Detailed Phase →](./methodology/01-asset-discovery.md)

---

# 2. DNS & Live Host Discovery

Not every discovered asset resolves or exposes a web service.

This phase determines which discovered assets are actually reachable.

## 2.1 DNS Resolution

🛠️ **Tool**: dnsx
```bash
dnsx -l ../phase1/assets_final.txt -a -resp -o dnsx.txt
```
Collect information such as:
```text
Hostname
IP Address
DNS Resolution
```
Output:
```text
dnsx.txt
```
---

## 2.2 HTTP/HTTPS Probing

🛠️ **Tool**: httpx
```bash
httpx -l ../phase1/assets_final.txt -silent -sc -title -tech-detect -ip -o httpx.txt
```
Collect:
```text
URL
Status Code
Title
Technology
IP
```
Output:

```text
httpx.txt
```
### Extract Live HTTP/HTTPS Hosts
```bash
grep -E '^https?://' httpx.txt > live_hosts.txt
```
The exact filtering may be adjusted depending on the output format of the installed version.

Output:
```text
live_hosts.txt
```
---

## 2.3 Status Code Analysis

Analyze response codes to identify interesting targets.

```text
200 → Accessible resource
301 → Redirect
302 → Redirect
401 → Authentication required
403 → Access restricted
404 → Resource not found
500 → Server-side error
503 → Service unavailable
```
A status code by itself is **not a vulnerability**.

The purpose of this phase is to identify assets that deserve further investigation.

## Questions

For every interesting host:
```text
Does it resolve?
Is it reachable?
HTTP or HTTPS?
Which status code?
What is the title?
What technology is exposed?
Which IP does it use?
```
## Final Output
```text
dnsx.txt
httpx.txt
live_hosts.txt
```
### Phase 2 Flow
```text
Asset Inventory
      ↓
DNS Resolution
      ↓
HTTP/HTTPS Probing
      ↓
Status / Title / IP / Technology
      ↓
Live Host Inventory
```
[Detailed Phase →](./methodology/02-dns-live-hosts.md)

---

# 3. Technology & Infrastructure

This phase identifies technologies, exposed ports, and services.

## 3.1 Technology Detection

🛠️ **Tool:** WhatWeb

### Basic
```bash
whatweb https://TARGET
```
### Verbose
```bash
whatweb -v https://TARGET
```
Save the result:
```text
whatweb.txt
```
Look for:
```text
Web Server
Framework
CMS
JavaScript Libraries
Backend Technologies
Headers
Application Characteristics
```
---

## 3.2 Port Discovery

🛠️ **Tool**: Naabu

### Single Host
```bash
naabu -host TARGET -top-ports 100
```
### Host List
```bash
naabu -list phase2/live_hosts.txt -top-ports 100 -o naabu.txt
```
Output:
```text
naabu.txt
```
Purpose:

- Identify exposed ports.
- Identify services that require deeper enumeration.

---
## 3.3 Service Enumeration

🛠️ **Tool:** Nmap

After identifying interesting ports:
```bash
nmap -sV -sC -p PORTS TARGET
```
Output:
```text
nmap.txt
```
The objective is to understand:
```text
Port
 ↓
Service
 ↓
Version
 ↓
Potentially Interesting Functionality
```
---

### SSH Enumeration

If TCP/22 is exposed and the program permits service enumeration:
```bash
nmap -p22 --script ssh2-enum-algos TARGET
```
---

## 3.4 Infrastructure Mapping

Correlate:
```text
Host
 ↓
IP
 ↓
Ports
 ↓
Services
 ↓
Technologies
```
Questions:

- What technologies are used?
- Which ports are exposed?
- Which services are running?
- Are there unusual services?
- Is SSH exposed?
- Are multiple applications sharing infrastructure?

## Final Output
```text
whatweb.txt
naabu.txt
nmap.txt
service-enumeration/
```
### Phase 3 Flow
```text
Live Hosts
     ↓
Technology Detection
     ↓
Port Discovery
     ↓
Service Enumeration
     ↓
Infrastructure Mapping
```
[Detailed Phase →](./methodology/03-tech-infrastructure.md)

---

# 4. Web Discovery

This phase focuses on discovering the application's web attack surface.

## 4.1 Web Crawling

🛠️ **Tools:** Katana, GoSpider, Hakrawler

### Katana

Single target:
```bash
katana -u https://TARGET -silent
```
Save output:

```bash
katana -u https://TARGET -silent -o katana.txt
```
Multiple live hosts:
```bash
katana -list ../phase2/live_hosts.txt -silent -o katana.txt
```
### JavaScript Crawling
```bash
katana -list ../phase2/live_hosts.txt -silent -jc -o katana-js.txt
```
Output:
```text
katana.txt
katana-js.txt
```
---

### GoSpider
```bash
gospider -s https://TARGET -o gospider
```
Output:
```text
gospider/
```
---

### Hakrawler
```bash
echo https://TARGET | hakrawler
```
Save when required:
```bash
echo https://TARGET | hakrawler > hakrawler.txt
```
---

## 4.2 Historical URL Discovery

🛠️ **Tools:** GAU, Waybackurls, Waymore

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
Historical URLs can reveal:
```text
Old Endpoints
Old Parameters
API Paths
Removed Functionality
Legacy Applications
Interesting Files
```
Historical data is treated as **candidate intelligence**.

An old URL is not automatically a current endpoint.

---

## 4.3 Results Combination

Keep every crawler and historical source separately.

```bash
cat katana.txt gospider.txt hakrawler.txt gau.txt wayback.txt waymore.txt > urls_raw.txt
```
Clean:
```bash
sed '/^$/d' urls_raw.txt | sort -u > urls_clean.txt
```
Output:
```text
katana.txt
katana-js.txt
gospider/
hakrawler.txt
gau.txt
wayback.txt
waymore/
urls_raw.txt
urls_clean.txt
```
---

## 4.4 Endpoint & Parameter Analysis

Analyze the collected URLs for:
```text
Endpoints
Parameters
API paths
Authentication endpoints
Administrative functionality
Upload functionality
Files
Configuration paths
Interesting response patterns
Development / staging references
```
Example parameter extraction:

```bash
grep "=" urls_clean.txt > parameters.txt
```
Output:
```text
parameters.txt
```
---

## 4.5 Content Discovery

🛠️ **Tools:** FFUF, Feroxbuster, Dirsearch, BFAC

### FFUF
```bash
ffuf -u https://TARGET/FUZZ -w WORDLIST
```
Save results when appropriate:
```bash
ffuf -u https://TARGET/FUZZ -w WORDLIST -o ffuf.json -of json
```
---

### Feroxbuster
```bash
feroxbuster -u https://TARGET -w WORDLIST
```
---

### Dirsearch
```bash
dirsearch -u https://TARGET
```
---

### BFAC

Use BFAC selectively when the target and discovered functionality justify it.

Content discovery should focus on identifying interesting resources rather than blindly scanning everything.

## Phase 4 Questions
```text
What functionality exists?
What endpoints exist?
What parameters exist?
What APIs exist?
What authentication surfaces exist?
What old functionality exists?
What hidden content exists?
```
### Phase 4 Flow
```text
Live Hosts
      ↓
Web Crawling
      ↓
Historical URLs
      ↓
Endpoint Collection
      ↓
Parameter Discovery
      ↓
Content Discovery
      ↓
URL / Endpoint Dataset
```
[Detailed Phase →](./methodology/04-web-discovery.md)

---

# 5. JavaScript Recon

Modern web applications often expose valuable application intelligence through JavaScript.

## 5.1 JavaScript Collection

🛠️ **Tools**: SubJS, Katana

### SubJS
```bash
subjs -i phase4/urls_clean.txt -o js.txt
```
Output:
```text
js.txt
```
### Katana JavaScript Collection
```bash
katana -list phase2/live_hosts.txt -silent -jc -o katana-js.txt
```
Output:
```text
katana-js.txt
```
---

## 5.2 JavaScript Analysis

🛠️ **Tool**: Mantra

Use Mantra to analyze collected JavaScript resources for:
```text
Endpoints
URLs
Interesting strings
Configuration
Potential secrets
Application logic references
```
The exact command depends on the installed Mantra version and local configuration.

---

## 5.3 Endpoint Extraction

Search JavaScript resources for endpoint-related patterns.

Example:
```bash
grep -Eio 'api|token|secret|key|password' file.js
```
Extract JavaScript references from HTML when needed:
```bash
grep -Eo 'src="[^"]+\.js[^"]*"' file.html
```
The goal is to discover:
```text
API endpoints
Internal routes
Hidden functionality
Parameters
Third-party integrations
Configuration references
```
---

## 5.4 Secrets & Interesting Data

🛠️ **Tool:** JSleak

Use JSleak to identify potentially exposed:
```text
API Keys
Tokens
Secrets
Interesting Credentials
Sensitive Configuration
```
### Important

A string that looks like a secret is **not automatically a valid credential**.

The finding must be analyzed and validated within the authorized scope.
```text
Potential Secret
      ↓
Identify
      ↓
Understand
      ↓
Validate
      ↓
Confirm
```
## Final Output
```text
js.txt
katana-js.txt
mantra results
jsleak results
```
### Phase 5 Flow
```text
JavaScript Collection
        ↓
JavaScript Analysis
        ↓
Endpoint Extraction
        ↓
Configuration Discovery
        ↓
Interesting Strings
        ↓
Potential Secrets
        ↓
Correlation
```
[Detailed Phase →](./methodology/05-javascript-recon.md)

---

# 6. Attack Surface Mapping

At this point, reconnaissance data from previous phases needs to become one structured attack surface.

## 6.1 Host Inventory

Record:
```text
Hostname
IP
Status
Title
Technology
Ports
Services
Application
Notes
```
Example:
```text
Host:
IP:
Status:
Title:
Technology:
Ports:
Services:
Notes:
```
---

## 6.2 Endpoint Inventory

Record:
```text
URL
Method
Endpoint
Parameter
Source
Authentication
Notes
```
Example:
```text
URL:
Method:
Endpoint:
Parameters:
Source:
Authentication:
Notes:
```
---

## 6.3 Parameter Mapping

Identify parameters from:

- Crawlers
- Historical URLs
- JavaScript
- APIs
- Manual observation

Examples:
```text
id
user
user_id
account
file
url
redirect
callback
search
query
```
The presence of a parameter does not indicate a vulnerability.

It identifies an input surface for later testing.

---

## 6.4 API Surface

Map:
```text
API Host
 ↓
API Endpoint
 ↓
HTTP Method
 ↓
Parameters
 ↓
Authentication
 ↓
Response
```
Interesting API surfaces may later become candidates for:
```text
Authorization Testing
IDOR / BOLA Testing
Authentication Testing
Input Validation Testing
Business Logic Testing
```
---

## 6.5 Authentication Surface

Identify:
```text
Login
Signup
Password Reset
Email Verification
Session Handling
OAuth
JWT
API Authentication
Account Management
Administrative Authentication
```
The purpose is to build a testing map for later security testing.

---

## Attack Surface Relationship
```text
Asset
 ↓
Host
 ↓
Application
 ↓
Technology
 ↓
Endpoint
 ↓
Parameter
 ↓
Authentication
 ↓
Potential Attack Surface
```
[Detailed Phase →](./methodology/06-attack-surface-mapping.md)

---

# 7. Public Exposure Recon

This phase investigates information publicly exposed by the organization.

## 7.1 GitHub Recon

🛠️ **Tools:** GitDorker, shhgit, git-wild-hunt

Look for:
```text
Public repositories
Source code
Configuration files
API references
Internal hostnames
Cloud references
Development information
Historical repositories
Sensitive paths
```
### GitDorker

Use GitDorker according to the installed version and the target's authorization.

Example structure:
```bash
python3 GitDorker.py -tf <github_token.txt> -q TARGET -d dorks.txt -o git_dorks_output.txt
Never expose GitHub tokens in the repository.
```
---

## 7.2 Secret Detection

🛠️ **Tool:** TruffleHog

Use TruffleHog only against repositories or organizations that are within the authorized scope.

Example:
```bash
trufflehog github --org=TARGET_ORG
```
The exact command may vary depending on the installed version.

Potential findings include:
```text
API Keys
Tokens
Passwords
Cloud Credentials
Private Keys
Other Sensitive Strings
```
### Validation
```text
Potential Secret
      ↓
Identify Source
      ↓
Understand Context
      ↓
Determine Validity
      ↓
Assess Impact
      ↓
Report if Confirmed
```
A potential secret is not automatically a valid credential or vulnerability.

---

## 7.3 Public Documents & Metadata

🛠️ **Tool:** MetaFinder

Investigate publicly available documents and metadata.

Look for:
```text
Author Information
Software Versions
Internal Paths
Document Metadata
Organization References
Potentially Sensitive Information
Public documents should be treated as intelligence that requires context.
```
---

## Public Exposure Validation
```text
Public Repository
≠ Vulnerability

Potential API Key
≠ Valid Credential

Historical URL
≠ Current Endpoint

Internal-looking Hostname
≠ Confirmed Internal Service

Metadata
≠ Sensitive Information
```
[Detailed Phase →](./methodology/07-public-exposure.md)

---

# 8. Recon Analysis & Handoff

This is the final reconnaissance phase.

The objective is to transform collected intelligence into a structured testing queue.

## 8.1 Correlation

Correlate information from:
```text
Phase 1
Phase 2
Phase 3
Phase 4
Phase 5
Phase 6
Phase 7
```
Example:
```bash
Subdomain
   ↓
DNS
   ↓
Live Host
   ↓
Technology
   ↓
Ports
   ↓
Application
   ↓
URLs
   ↓
Endpoints
   ↓
Parameters
   ↓
Authentication

```
---

## 8.2 Validation

Recon findings should be classified according to their confidence.
```text
Discovered
   ↓
Observed
   ↓
Reproduced
   ↓
Validated
   ↓
Confirmed
```bash
Examples:
```text
Subdomain discovered
→ Candidate asset

Port discovered
→ Exposed service candidate

Interesting endpoint discovered
→ Testing candidate

Potential secret discovered
→ Requires validation

Scanner finding
→ Requires reproduction
```
---

## 8.3 Prioritization

Prioritization means deciding what should be manually investigated first.

It does not mean declaring something vulnerable.

Prioritize surfaces such as:
```text
Authentication
Authorization
APIs
Sensitive Parameters
Administrative Functions
File Uploads
Interesting Technologies
Potentially Exposed Information
High-value Application Functionality
```
---

## 8.4 Testing Queue

Create a structured queue:
```text
Target
Host
Endpoint
Method
Parameter
Technology
Authentication
Source
Why Interesting
Next Action
Status
Notes
```
Example:
```text
Target:
Host:
Endpoint:
Method:
Parameter:
Technology:
Authentication:
Source:
Why Interesting:
Next Action:
Status:
Notes:
```
---

# Recon Finding Lifecycle

Every interesting discovery should follow:
```text
Discover
   ↓
Reproduce
   ↓
Understand
   ↓
Validate Impact
   ↓
Confirm
   ↓
Report
```
Do not report a vulnerability based only on:

- Scanner output.
- A status code.
- An exposed endpoint.
- A hostname.
- A JavaScript string.
- A historical URL.
- A potential secret.

These are starting points for investigation.

---

# Output Philosophy

Every important tool should produce a separat output before results are merged.

Example:
```text
subfinder.txt
amass.txt
assetfinder.txt
crt.txt
```
Then:
```text
Merge
 ↓
Normalize
 ↓
Deduplicate
 ↓
Compare
 ↓
Analyze
```
This allows:

- Source comparison.
- Unique discovery identification.
- Reproducibility.
- Evidence preservation.
- Troubleshooting.
- Better correlation.

---

# Tool Philosophy

Every tool should answer a question.
```text
What assets exist?
        ↓
Asset Discovery

Which assets resolve?
        ↓
DNS

Which assets expose web services?
        ↓
HTTP Probing

What technologies are used?
        ↓
Technology Detection

Which ports are exposed?
        ↓
Port Discovery

Which services are running?
        ↓
Service Enumeration

What functionality exists?
        ↓
Crawling / Content Discovery

What does JavaScript reveal?
        ↓
JavaScript Recon

What is publicly exposed?
        ↓
Public Exposure Recon

How are all discoveries connected?
        ↓
Attack Surface Mapping

What should be tested?
        ↓
Analysis & Handoff
```
---

# Recon Mindset

Do not think:

> Which tool should I run next?

Think:

> What do I need to know about this target?

The methodology follows:
```text
Question
   ↓
Tool
   ↓
Output
   ↓
Analysis
   ↓
Correlation
   ↓
Decision
```
This prevents reconnaissance from becoming a collection of random commands.

---

# Final Recon Dataset

The final dataset should contain structured information about:
```text
Assets
Hosts
IPs
Technologies
Ports
Services
Applications
URLs
Endpoints
Parameters
APIs
Authentication Surfaces
JavaScript Intelligence
Public Exposure
Interesting Assets
Testing Candidates
```
---

# Recommended Workspace Structure
```text
TARGET/
│
├── phase0/
│   └── scope-notes.md
│
├── phase1/
│   ├── subfinder.txt
│   ├── amass.txt
│   ├── assetfinder.txt
│   ├── crt.txt
│   ├── assets_raw.txt
│   ├── assets_clean.txt
│   └── assets_final.txt
│
├── phase2/
│   ├── dnsx.txt
│   ├── httpx.txt
│   └── live_hosts.txt
│
├── phase3/
│   ├── whatweb.txt
│   ├── naabu.txt
│   ├── nmap.txt
│   └── service-enumeration/
│
├── phase4/
│   ├── katana.txt
│   ├── katana-js.txt
│   ├── gospider/
│   ├── hakrawler.txt
│   ├── gau.txt
│   ├── wayback.txt
│   ├── waymore/
│   ├── urls_raw.txt
│   ├── urls_clean.txt
│   ├── parameters.txt
│   └── content-discovery/
│
├── phase5/
│   ├── js.txt
│   ├── katana-js.txt
│   ├── mantra/
│   └── jsleak/
│
├── phase6/
│   └── attack-surface.md
│
├── phase7/
│   ├── github/
│   ├── trufflehog/
│   └── metadata/
│
└── phase8/
    ├── recon-report.md
    ├── testing-queue.md
    └── final-dataset/
```
---
# Repository Structure
```text
WebRecon-Methodology/
│
├── docs/
│   └── methodology-notes.md
│
├── methodology/
│   ├── 00-scope.md
│   ├── 01-asset-discovery.md
│   ├── 02-dns-live-hosts.md
│   ├── 03-tech-infrastructure.md
│   ├── 04-web-discovery.md
│   ├── 05-javascript-recon.md
│   ├── 06-attack-surface-mapping.md
│   ├── 07-public-exposure.md
│   └── 08-analysis-handoff.md
│
├── templates/
│   ├── attack-surface.md
│   ├── recon-report.md
│   └── target-notes.md
│
├── tools/
│   └── recon-tools.md
│
├── mrecon.png
└── README.md
```
---

# Templates

Reusable templates are included for documenting reconnaissance.

- [Attack Surface Template](./templates/attack-surface.md)
- [Recon Report Template](./templates/recon-report.md)
- [Target Notes Template](./templates/target-notes.md)

---

# Documentation

| Document | Description |
|---|---|
| [Methodology Notes](./docs/methodology-notes.md) | Core principles and design decisions |
| [Scope & Target Preparation](./methodology/00-scope.md) | Scope and authorization |
| [Asset Discovery](./methodology/01-asset-discovery.md) | Passive asset discovery |
| [DNS & Live Hosts](./methodology/02-dns-live-hosts.md) | DNS and HTTP discovery |
| [Technology & Infrastructure](./methodology/03-tech-infrastructure.md) | Technologies, ports and services |
| [Web Discovery](./methodology/04-web-discovery.md) | Crawling, URLs and content discovery |
| [JavaScript Recon](./methodology/05-javascript-recon.md) | JavaScript intelligence |
| [Attack Surface Mapping](./methodology/06-attack-surface-mapping.md) | Attack surface correlation |
| [Public Exposure](./methodology/07-public-exposure.md) | Public information and exposure |
| [Analysis & Handoff](./methodology/08-analysis-handoff.md) | Final analysis and testing queue |
| [Recon Tools](./tools/recon-tools.md) | Tool-to-phase reference |

---

# Scope & Authorization

This methodology is intended for:

- Authorized Bug Bounty Programs
- Authorized Security Assessments
- Lab Environments
- Systems where explicit permission exists

Always follow:

- Program scope.
- Program policy.
- Rate limits.
- Testing restrictions.
- Automation restrictions.
- Authentication requirements.
- Reporting requirements.

Never test assets outside the authorized scope.

---

# Status

**WebRecon Methodology — Initial Version**

This methodology is continuously refined through practical Web Application Security and Bug Bounty research.

---

<p align="center">
  <sub>Structured reconnaissance. Better attack-surface visibility. Disciplined security testing.</sub>
</p>
