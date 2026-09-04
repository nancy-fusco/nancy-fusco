[← Back to Home](./index.html)

# Architecture Case Study: Enterprise Single-Sourcing & Multi-Target Delivery Pipeline

**Tooling Ecosystem:** MadCap Flare, Zoomin Software, Fluid Topics, Git, XML/XHTML  
**Core Methodologies:** Topic-Based Authoring (EPPO), Single-Sourcing, Dynamic Ingestion, Faceted Taxonomy  
**Target Domain:** Enterprise FinTech & Core Banking Platforms  

---

## Executive Summary
Large enterprise documentation systems face a fundamental delivery problem: a single feature modification often requires simultaneous updates across administrative PDF runbooks, online help systems, partner API portals, and customer-facing knowledge bases. 

This case study details the information architecture and continuous delivery pipeline I designed to unify multi-variant product content using **MadCap Flare** as the centralized single-source engine, compiling into automated HTML5 bundles ingested by **Zoomin** and **Fluid Topics** for dynamic, role-based delivery.

---

## The Challenge: Content Drift & Redundant Maintenance
Prior to consolidating the authoring pipeline, product documentation suffered from common enterprise friction points:
* **Manual Duplication:** Writers maintained parallel versions of the same procedural guides across Word, static HTML pages, and PDF manuals, leading to version drift and outdated compliance steps.
* **Coarse-Grained Search:** End users struggled to find relevant content in static 300-page manuals that lacked faceted search, version filtering, or role-based access controls.
* **Publishing Latency:** Recompiling and manually uploading individual documentation changes took days, lagging behind bi-weekly engineering sprint releases.

---

## Architectural Strategy: Single-Sourcing Hierarchy

To achieve strict content reuse, I architected a modular repository structure within MadCap Flare that separated raw source content from target-specific delivery rules:

    ClearVault-Flare-Project/
    ├── Content/
    │   ├── Concepts/               # EPPO conceptual overviews
    │   ├── Procedures/             # Modular task-based topics
    │   └── Resources/
    │       ├── Snippets/           # Transcludable text & code blocks (.flsnp)
    │       ├── TableStyles/        # Standardized responsive tables
    │       └── Variables/          # Global tokens (Products, Dates, Roles)
    ├── Project/
    │   ├── ConditionTagSets/       # Audience, Platform, & Market tags (.flcts)
    │   ├── Targets/                # Build targets (PDF Admin, HTML5 Zoomin)
    │   └── TOCs/                   # Output-specific master tables of contents

### 1. Granular Condition Tagging
I defined orthogonal condition tag sets to filter content dynamically at build time rather than maintaining duplicate files:
* **Audience:** `Audience.InternalOnly`, `Audience.Administrator`, `Audience.Borrower`
* **Delivery Channel:** `Channel.PrintOnly` (page breaks, explicit URLs), `Channel.WebOnly` (accordions, embedded search hints)
* **Market Compliance:** `Region.US_Conventional`, `Region.Federal_FHA`

### 2. Transclusion via Snippets & System Variables
Standardized warnings, regulatory disclosure statements (such as RESPA Section 10 rules), and API endpoint headers were decoupled into shared `.flsnp` files. Updating a single compliance snippet instantly propagated the change across all downstream PDF runbooks and dynamic web targets.

---

## The Ingestion Pipeline: Flare to Zoomin / Fluid Topics

Rather than publishing static web pages to isolated web servers, the compilation pipeline transformed raw topics into structured, taxonomy-indexed delivery packages:

    [ MadCap Flare Source ]
               |
               v  (Compile HTML5 Target Engine)
    [ HTML5 Build Artifacts ]
      ├── CSH Identifiers & Alias Files
      ├── Faceted Taxonomy Metadata (.xml)
      └── Minified Web Assets (JS/CSS/SVGs)
               |
               v  (Automated ZIP Bundling)
    [ Distribution Bundle (.zip) ]
               |
               v  (Ingestion API / SFTP Portal)
    [ Zoomin / Fluid Topics Engine ]
               |
               +---> Semantic Content Classification
               +---> Faceted Search Indexing (Product, Version, Role)
               +---> Role-Based Access Control (Entitlements)
               |
               v
    [ Live Enterprise Help Portal ]

### Step 1: Target Definition & Skin Optimization
* Configured Flare's **HTML5 Side-Navigation Target** with clean, unbranded skins to allow Zoomin's styling layer to govern typography and branding.
* Enabled **Micro-Content** definitions within Flare to supply search engines with targeted question-and-answer response boxes.

### Step 2: Taxonomy & Classification Tagging
To power faceted filtering in Zoomin and Fluid Topics, I embedded semantic taxonomy metadata into topic headers. This ensured that search queries could be faceted dynamically:
* `product_suite`: ClearVault Servicing
* `user_persona`: Escrow Manager, Integration Lead
* `compliance_regime`: CFPB, RESPA, TILA
* `content_type`: Task Guide, Architectural Specification, Release Note

### Step 3: Bundle Assembly & Ingestion
1. Post-build routines compressed the Flare output directory into a standardized distribution archive (`ClearVault_Servicing_v3.4_en-US.zip`).
2. The archive was uploaded to Zoomin's ingestion pipeline, which parsed the alias mapping files, verified TOC node references, and extracted topic-level metadata.
3. Once validated, Zoomin published the topics to the unified content portal, automatically linking related release notes and updating the search index with zero system downtime.

---

## Underlying Source Example: Flare XML / XHTML Markup

In Flare's internal code view, topics combine XHTML5 with proprietary XML schema namespaces. Below is an authentic snippet demonstrating conditional inclusion and snippet transclusion:

    <?xml version="1.0" encoding="utf-8"?>
    <html xmlns:MadCap="http://www.madcapsoftware.com/Schemas/MadCap.xsd" 
          MadCap:lastBlockDepth="4" 
          MadCap:lastHeight="320" 
          MadCap:lastWidth="850">
        <head>
            <meta name="product_suite" content="ClearVault" />
            <meta name="user_persona" content="Escrow_Admin" />
        </head>
        <body>
            <h1>Configuring Automated Escrow Reserves</h1>
            <MadCap:snippetBlock src="../Resources/Snippets/Common/Prerequisites-Notice.flsnp"/>
            
            <p>Ensure the reserve balance complies with 
               <MadCap:variable name="Compliance.FederalStandard"/> guidelines.
            </p>

            <p MadCap:conditions="Audience.Administrator">
               Internal AP clearing ledgers must be verified prior to initiating run queues.
            </p>
        </body>
    </html>

---

## Measurable Operational Outcomes

* **Single-Source Efficiency:** Reduced core authoring maintenance overhead by **60%** by eliminating duplicated content across multiple release formats.
* **Search Precision:** Zoomin’s faceted search classification reduced customer support escalations regarding misplaced regulatory procedures by **45%**.
* **Rapid Deployment:** Publishing cycle times dropped from 3 business days of manual compilation to an automated, push-button build-and-bundle workflow completed in under **20 minutes**.

---

## Related Portfolio Samples
* [System Specification: ClearVault Loan Event Integration](./clearvault-spec.html)
* [Task Guide: Configuring Automated Escrow Analysis](./clearvault-task-guide.html)
* [AI-Accelerated Legacy Documentation Transformation](./content-architecture.html)
