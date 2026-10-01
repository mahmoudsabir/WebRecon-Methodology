# Recon Tools

## Purpose

This file defines the core tools used throughout the Web Bug Bounty Recon Methodology.

It explains where each tool belongs, what it is used for, what information it provides, and how its output contributes to the next step.

Detailed workflows and commands are documented in the corresponding methodology files.


---

# 1. Asset Discovery

### Goal

Discover candidate assets from multiple passive sources.

### Core Tools

#### Subfinder
Role: Primary passive subdomain discovery.

Provides:
- Candidate subdomains
- API / staging / development hosts
- Naming patterns

Feeds into: Asset collection and comparison.

---

#### Amass
Role: Additional passive discovery source.

Provides:
- Additional candidate subdomains
- Infrastructure-related names
- Unique discoveries

Feeds into: Asset comparison.

---

#### Assetfinder
Role: Independent subdomain discovery.

Provides:
- Candidate subdomains
- Unique or shared assets

Feeds into: Asset comparison.

---

#### crt.sh
Role: Certificate Transparency discovery.

Provides:
- Certificate-associated hostnames
- Historical or alternative hostnames
- Potential staging/development names

Feeds into: Asset collection and scope validation.

> CT results are candidate assets and must still be checked against scope.


### Flow

Subfinder + Amass + Assetfinder + crt.sh
→ Raw Assets
→ Normalize
→ Deduplicate
→ Compare
→ Final Asset Inventory


---

# 2. DNS & Live Host Discovery

### Goal

Determine which assets resolve and which expose reachable web services.

### Core Tools

#### dnsx
Role: DNS resolution and validation.

Provides:
- Resolvable hosts
- IP addresses
- DNS information

Feeds into: Live-host and infrastructure analysis.

---

#### httpx
Role: HTTP/HTTPS probing.

Provides:
- Live web services
- Status codes
- Titles
- Technologies
- IP addresses
- Redirects

Feeds into: Live Host Inventory and Phase 3.


### Flow

Final Asset Inventory
→ dnsx
→ httpx
→ Live Hosts
→ Interesting Hosts


---

# 3. Technology & Infrastructure

### Goal

Identify technologies, ports, services, and infrastructure characteristics.

### Core Tools

#### WhatWeb
Role: Web technology fingerprinting.

Provides:
- Servers
- Frameworks
- CMS
- Libraries
- Technology indicators

Feeds into: Technology analysis and testing decisions.

---

#### Naabu
Role: Port discovery.

Provides:
- Open ports
- Non-standard web ports
- Potentially interesting services

Feeds into: Nmap service enumeration.

---

#### Nmap
Role: Service enumeration.

Provides:
- Service names
- Versions
- Script results
- Service characteristics

Feeds into: Infrastructure mapping and deeper analysis.


### Flow

Live Hosts
→ WhatWeb + Naabu
→ Nmap
→ Technology & Infrastructure Map


---

# 4. Web Discovery

### Goal

Discover URLs, endpoints, parameters, links, and historical attack surface.

### Core Tools

#### Katana
Role: Primary web crawler.

Provides:
- URLs
- Endpoints
- Parameters
- JavaScript references

Feeds into: URL inventory and JavaScript Recon.

---

#### GoSpider
Role: Independent web crawler.

Provides:
- URLs
- Links
- Parameters
- JavaScript references

Feeds into: URL comparison.

---

#### Hakrawler
Role: Lightweight crawler.

Provides:
- URLs
- Links
- Parameters
- Application paths

Feeds into: URL correlation.

---

#### GAU
Role: Historical URL discovery.

Provides:
- Old endpoints
- Parameters
- Legacy paths
- Old files

Feeds into: Historical attack surface analysis.

---

#### Waybackurls
Role: Web archive URL discovery.

Provides:
- Archived endpoints
- Old paths
- Historical parameters

Feeds into: Historical endpoint analysis.

---

#### Waymore
Role: Extended historical discovery.

Provides:
- Historical URLs
- Files
- Endpoints
- Parameters

Feeds into: Historical attack surface analysis.


### Flow
Live Web Assets
→ Crawlers
→ Historical Sources
→ URL Collection
→ Normalize / Deduplicate
→ Endpoint & Parameter Analysis


---

# 5. Content Discovery

### Goal

Discover resources that may not be visible through normal crawling.

### Core Tools

#### FFUF
Role: Content fuzzing.

Provides:
- Hidden paths
- Files
- Directories
- Response characteristics

Feeds into: Manual validation.

---

#### Feroxbuster
Role: Content and recursive discovery.

Provides:
- Paths
- Files
- Directories

Feeds into: Content inventory.

---

#### Dirsearch
Role: Directory and file discovery.

Provides:
- Hidden paths
- Files
- Directories
- Administrative/configuration resources

Feeds into: Content comparison and validation.


### Flow

Interesting Web Targets
→ FFUF + Feroxbuster + Dirsearch
→ Discovered Content
→ Compare
→ Manual Validation


---

# 6. JavaScript Recon

### Goal

Extract application intelligence from client-side JavaScript.

### Core Tools

#### SubJS
Role: JavaScript collection.

Provides:
- JS URLs
- Application bundles
- Client-side resources

Feeds into: JavaScript analysis.

---

#### Mantra
Role: JavaScript analysis.

Provides:
- Endpoints
- URLs
- Paths
- Parameters
- Interesting strings

Feeds into: Attack Surface Mapping.

---

#### JSleak
Role: Potential secret discovery.

Provides:
- API keys
- Tokens
- Credentials
- Sensitive strings

Feeds into: Public exposure analysis and validation.

> Detected values are candidates and require safe validation.


### Flow

JS Collection
→ SubJS
→ Mantra + JSleak
→ Endpoints / Intelligence / Potential Secrets


---

# 7. Attack Surface Mapping

### Goal

Combine reconnaissance results into a structured attack surface.

### Core Inputs

- Assets
- DNS
- Live Hosts
- Technologies
- Ports
- Services
- URLs
- Endpoints
- Parameters
- APIs
- Authentication surfaces
- JavaScript findings
- Public exposure

### Process

Collect
→ Correlate
→ Classify
→ Map Relationships
→ Identify Interesting Assets
→ Build Testing Queue


---

# 8. Public Exposure Recon

### Goal

Identify information publicly exposed through code, repositories, documents, and metadata.

### Core Tools

#### GitDorker
Role: GitHub reconnaissance.

Provides:
- Public code references
- Configuration files
- Internal hostnames
- API references
- Potential secrets

Feeds into: Public exposure analysis.

---

#### shhgit
Role: Git secret discovery.

Provides:
- API keys
- Tokens
- Credentials
- Private keys

Feeds into: Secret validation.

---

#### git-wild-hunt
Role: Additional GitHub exposure discovery.

Provides:
- Secrets
- Tokens
- Internal URLs
- Configuration information

Feeds into: Public exposure correlation.

---

#### TruffleHog
Role: Dedicated secret scanning.

Provides:
- API keys
- Access tokens
- Cloud credentials
- Private keys
- Database credentials

Feeds into: Secret validation and exposure classification.

> A scanner result is a candidate finding, not automatically a vulnerability.

---

#### MetaFinder
Role: Document metadata reconnaissance.

Provides:
- Author information
- Software information
- Internal paths
- Document metadata

Feeds into: Public exposure analysis.


### Flow

Public Code + Documents
→ GitHub Recon + Secret Scanning + Metadata
→ Potential Exposure
→ Classification
→ Correlation with Attack Surface


---

# 9. Core Execution Principles

1. Every core tool has a defined phase and purpose.
2. Run the important tools for the phase and save each output separately.
3. Analyze individual results before merging.
4. Normalize, merge, deduplicate, and compare results.
5. Tool output is intelligence, not automatically a vulnerability.
6. Feed analyzed results into the next phase.

### Standard Pattern

Tool
→ Output
→ Analyze
→ Correlate
→ Decision
→ Next Phase


---

# 10. Phase-to-Tool Reference
| Phase | Core Tools |
|---|---|
| Phase 0 — Scope | curl, cat, grep, sed, awk |
| Phase 1 — Asset Discovery | Subfinder, Amass, Assetfinder, crt.sh |
| Phase 2 — DNS & Live Hosts | dnsx, httpx |
| Phase 3 — Technology & Infrastructure | WhatWeb, Naabu, Nmap |
| Phase 4 — Web Discovery | Katana, GoSpider, Hakrawler, GAU, Waybackurls, Waymore |
| Phase 4 — Content Discovery | FFUF, Feroxbuster, Dirsearch |
| Phase 5 — JavaScript Recon | SubJS, Mantra, JSleak |
| Phase 6 — Attack Surface Mapping | Analysis / Correlation |
| Phase 7 — Public Exposure | GitDorker, shhgit, git-wild-hunt, TruffleHog, MetaFinder |
| Phase 8 — Analysis & Handoff | Analysis / Correlation |
