# Phase 6 — Attack Surface Mapping

## Objective

Transform the results collected from the previous reconnaissance phases
into a structured view of the target's web attack surface.

The goal is to understand what exists, where it exists, and how the
different assets and application components relate to each other.

This phase does not perform vulnerability testing.

---

## Scope

This phase focuses on organizing and correlating:

- Hosts
- DNS information
- Live web applications
- Technologies
- Ports
- Services
- URLs
- Endpoints
- Parameters
- APIs
- Authentication surfaces
- JavaScript-discovered resources
- Interesting assets

---

## Input

Primary inputs come from previous phases:

```text
Phase 1
assets_final.txt

Phase 2
dnsx.txt
httpx.txt
live_hosts.txt

Phase 3
whatweb.txt
naabu.txt
nmap.txt

Phase 4
urls_clean.txt
content-discovery results

Phase 5
js.txt
js-endpoints.txt
js-urls.txt
js-config.txt
js-interesting.txt
