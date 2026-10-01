# Phase 1 — Asset Discovery

## Objective

Discover candidate assets associated with the authorized target using
multiple passive asset-discovery sources.

The goal of this phase is to build a comprehensive candidate asset
inventory before moving to DNS resolution and live-host discovery.

> Asset discovery does not confirm that an asset is live, reachable,
> or currently in use.

---

## Scope

This phase focuses on:

- Subdomain discovery
- Passive asset discovery
- Certificate Transparency
- Result collection
- Normalization
- Deduplication
- Source comparison
- Final candidate asset inventory

DNS resolution and HTTP probing are handled in Phase 2.

---

## Input

The input comes from Phase 0:

- Authorized target domain(s)
- Confirmed scope
- Program restrictions
- Automation/rate-limit requirements

Example:

```text
TARGET=example.com
