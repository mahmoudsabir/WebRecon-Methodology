# Methodology Notes

## Purpose

This file contains general notes, principles, and design decisions behind the Recon Methodology.

It is not an execution guide.

Detailed execution is documented inside the methodology files.


---

# Core Philosophy

The methodology follows:

Scope
→ Discover
→ Validate
→ Enumerate
→ Correlate
→ Map
→ Analyze
→ Handoff


---

# Recon Mindset

Recon is not about collecting the largest amount of data.

The objective is to build an accurate understanding of the target's attack surface.

Every result should answer at least one useful question about the target.


---

# Tool Philosophy

Tools are used to collect information.

They do not replace analysis.

Standard approach:

Tool
→ Output
→ Analysis
→ Correlation
→ Decision


---

# Output Philosophy

Each important tool should have its own output.

Raw results should be preserved before:

- Cleaning
- Normalization
- Deduplication
- Merging
- Comparison


---

# Correlation

Information becomes more useful when different sources are compared.

Examples:

Subdomain Sources
→ Asset Correlation

DNS + HTTP
→ Live Host Validation

Technology + Ports
→ Infrastructure Understanding

Crawlers + Historical Sources
→ Endpoint Coverage

JavaScript + URLs
→ Application Mapping

GitHub + Assets
→ Public Exposure Correlation


---

# Validation

Recon results should be treated according to their confidence.

### Candidate

Discovered by a tool but not yet validated.

### Observed

Confirmed through another source or direct observation.

### Validated

Confirmed to be relevant to the target and authorized scope.

### Security Finding

Requires separate security testing and impact validation.


---

# Scope Principle

Scope is the first and final boundary.

A technically interesting asset is not automatically a target.

Before deeper investigation:

Scope
→ Authorization
→ Target
→ Technique


---

# Efficiency

The methodology should avoid unnecessary execution.

Use the information already collected to determine what deserves deeper investigation.

More tools do not automatically mean better reconnaissance.


---

# Manual Analysis

Automation is used to increase coverage and reduce repetitive work.

Important results should still be manually reviewed.

The final objective is understanding the application, not simply generating tool output.


---

# Handoff

Recon ends when the available information is sufficient to build a reliable testing queue.

Final flow:

Recon Data
→ Attack Surface
→ Interesting Targets
→ Testing Queue
→ Manual Security Testing


---

# Design Decisions

- Keep the methodology phase-based.
- Preserve individual tool outputs.
- Use multiple sources for coverage and comparison.
- Separate reconnaissance from vulnerability validation.
- Keep scope verification throughout the process.
- Avoid unnecessary tooling and redundant steps.
- Prefer useful intelligence over raw data volume.
