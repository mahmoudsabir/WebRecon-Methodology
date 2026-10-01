# Phase 2 — DNS & Live Host Discovery

## Objective

Determine which candidate assets discovered during Phase 1 resolve
through DNS and which hosts are reachable over HTTP/HTTPS.

This phase transforms the candidate asset inventory into a list of
validated DNS and live web assets.

---

## Scope

This phase focuses on:

- DNS resolution
- IP discovery
- DNS record information
- HTTP/HTTPS probing
- Status codes
- Page titles
- Server information
- Technology hints
- Live host classification

Port scanning and deeper service enumeration are handled in Phase 3.

---

## Input

The primary input is:

```text
phase1/assets_final.txt
