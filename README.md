# Enterprise Technical Documentation & Content Architecture Portfolio

Live Site: https://nancy-fusco.github.io

Author: Nancy Fusco  
Role: Senior Technical Writer & Content Architect  
Standards: Microsoft Style Guide | Every Page is Page One (EPPO) | Docs-as-Code  

---

## Repository Overview
This repository contains production-grade technical documentation samples, system specifications, and content architecture case studies tailored for enterprise software, commercial lending (ClearVault), and payment infrastructure (ApexPay).

All content is authored in GitHub-Flavored Markdown and deployed continuously via GitHub Pages using the Jekyll static site generator.

---

## Content Architecture & Document Suites

### 1. ClearVault Lending Suite
Focus: Enterprise loan servicing, secondary marketing, and mortgage regulatory compliance.
* clearvault-release-notes.md — Major release notes covering automated escrow analysis, dynamic TRID/TILA disclosures, and breaking database schema changes.
* clearvault-task-guide.md — Procedural task guide for configuring automated annual escrow runs, RESPA cushion limits, and Accounts Payable batch exports.

### 2. ApexPay Payment Gateway Suite
Focus: Transaction clearing, batch processing, and secure integration infrastructure.
* release-notes.md — Enterprise platform update detailing in-memory X9.37 check settlement reconciliation and scoped OAuth2 token models.
* task-guide.md — Self-contained procedural topic walking through automated image cash letter (ICL) pipeline configuration and Type 01/99 control verification.

### 3. Systems Architecture & AI Workflows
Focus: ContentOps scalability, legacy manual decomposition, and model orchestration.
* content-architecture.md — Case study detailing an AI-augmented pipeline (Claude, Copilot) used to deconstruct a 650-page monolithic banking manual across 120+ modules into EPPO topics.
* app-architecture.md — Technical specification and admin guide for an AI-powered media catalog built in Google AI Studio, defining tabular data contracts and prompt grounding rules.

---

## Authoring Standards & Editorial Style

* Microsoft Writing Style Guide: User-focused minimalism, active voice, scannable structures, and plain language principles.
* Every Page is Page One (EPPO): Standalone topic architecture ensuring topics provide context, explicit prerequisites, and clear post-requisites without requiring linear chapter reading.
* Semantic Markdown: Pure, lint-clean Markdown designed for multi-channel publishing and continuous deployment.

---

## Technical Stack & Tooling

* Engine: Jekyll (jekyll-theme-primer)
* Hosting / CI/CD: GitHub Pages via automated deployment workflows
* Styling: Custom SCSS (assets/css/style.scss)
* Source Control: Git (branching, commits, pull requests)
* Authoring: Markdown, VS Code

---

## Local Development & Contribution

To run and preview this site locally:

1. Clone the repository:
   git clone https://github.com/your-username/your-repo.git

2. Navigate to the root directory:
   cd your-repo

3. Launch with Jekyll:
   bundle exec jekyll serve

4. Open your browser to http://localhost:4000
