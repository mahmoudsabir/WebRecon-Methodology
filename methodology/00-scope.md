# 00 — Scope & Target Preparation

## Goal

Define exactly what can be tested before starting reconnaissance.

The objective is to understand the Bug Bounty program rules, identify
in-scope assets, identify out-of-scope assets, and record testing
restrictions.

---

## Inputs

- Program URL
- Program policy
- Scope definition
- Target/domain
- Authentication requirements
- Testing restrictions
- Rate limits
- Automation restrictions

---

## Tools

### Core

- curl
- cat
- grep
- sed
- awk

No active reconnaissance tools are required at this stage.

---

## Workflow

Program
   ↓
Read Rules
   ↓
Define Scope
   ↓
Identify Target Type
   ↓
Define Restrictions
   ↓
Ready for Recon

---

## Scope Verification

Record:

- Program name
- Program URL
- In-scope domains
- In-scope subdomains
- In-scope applications
- In-scope APIs
- Out-of-scope assets
- Third-party assets
- Testing restrictions

Never assume that an asset is in scope without sufficient evidence
from the program policy.

---

## Testing Restrictions

Record:

- Rate limits
- Automation restrictions
- Authentication requirements
- Prohibited actions
- Prohibited vulnerability classes
- Sensitive-data restrictions
- Account requirements
- Safe-testing requirements

---

## Decision Points

### Asset Scope

Asset
  ↓
Clearly In-Scope?
  ├── YES → Continue
  └── NO / UNCLEAR → Do Not Test

### Subdomain Scope

Subdomain
  ↓
Explicitly or clearly covered by scope?
  ├── YES → Continue
  └── NO / UNCLEAR → Verify before testing

### Automation

Automation Allowed?
  ├── YES → Use within program limits
  └── NO / UNCLEAR → Avoid automated testing

---

## Output

Create a scope record containing:

- Program
- Target
- In-Scope assets
- Out-of-Scope assets
- Restrictions
- Authentication requirements
- Rate limits
- Automation rules
- Important notes

---

## Exit Criteria

Phase 0 is complete only when the following questions can be answered:

1. What am I allowed to test?
2. What am I not allowed to test?
3. Which assets are in scope?
4. Which assets are out of scope?
5. What testing techniques are restricted?
6. What are the rate limits?
7. Is automation allowed?
8. Is authentication required?

If a critical scope question remains unclear, stop and verify the
program policy before continuing.

---

## Handoff

Phase 0
   ↓
Approved Target
   ↓
Phase 1 — Asset Discovery
