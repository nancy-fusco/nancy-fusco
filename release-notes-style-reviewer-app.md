# Release Notes Style & Quality Reviewer — User Guide
The Release Notes Style & Quality Reviewer is an automated quality assurance and technical editing tool designed for technical documentation teams, product managers, and release engineers. It critiques release notes against the Microsoft Writing Style Guide and custom House Rules, computing a binary Clean Page Metric and generating an actionable, page-by-page remediation report.

---

## Key Concepts & Scoring Methodology

### The Clean Page Metric
Unlike line-item word checkers, this reviewer evaluates quality through a binary page-level integrity model:
* 0 Violations on a page = Clean Page
* 1 or more Violations on a page = Flagged Page

The overall score is calculated as:
Integrity Tiers
* 90% to 100%: High Quality Integrity — Document meets editorial standards and is ready for release.
* 70% to 89%: Revisions Recommended — Minor style violations or house rule deviations requiring review.
* Below 70%: Substantial Violations — Systematic quality issues requiring editorial restructuring before publication.

---

### House Rule Veto Priority
Technical documentation often requires project-specific exceptions to standard style manuals (e.g., specific brand naming conventions, mandatory legal disclaimers, or customized formatting).
* Primary Authority: House Rules always override the Microsoft Writing Style Guide.
* Conflict Resolution: If a House Rule conflicts with a Microsoft Style recommendation, the House Rule always wins. The engine will never penalize compliance with a documented House Rule.

---

### Exclusion & Auto-Pass Logic
To avoid false positives and noise, the engine automatically applies domain-specific filters:
* Table of Contents (TOC): Automatically detected and assigned an automatic Clean Pass.
* Legal & Copyright Notices: Automatically detected and assigned an automatic Clean Pass.
* Technical Identifiers: Terminal commands, CLI flags (--verbose), file system paths (/var/log/...), environment variables, code snippets, and database column names are automatically exempt from prose style critique.

---

## Getting Started & Ingestion Workflow

### Supported File Formats
1. Release Notes:
* PDF (.pdf): Extracted per page with layout blocks, headings, and visual coordinates.
* Plain Text (.txt): Parsed using page delimiters (---- Page N ----).
2. House Rules:
* Text file (.txt): Plain text listing one rule or guideline per line/paragraph.

---

### Uploading Documents & House Rules

