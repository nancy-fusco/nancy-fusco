[← Back to Home](./index.html)

# Release Notes: ApexPay Gateway v4.2.0

**Release Date:** September 2026  
**Build Version:** 4.2.0-b114  
**Target Audience:** Platform Administrators, Solutions Architects, Integration Engineers  
**Deployment Impact:** Scheduled downtime required (approx. 15 minutes for schema migration)

---

## Executive Summary
ApexPay Gateway v4.2.0 delivers automated transaction reconciliation for high-volume check and settlement batches, introduces granular API token scoping, and expands webhook audit retention. This release includes one breaking change affecting legacy X9.37 file imports without explicit header timestamps.

---

## What's New

### 1. Automated Batch Reconciliation Engine
Operational teams can now automate reconciliation between inbound clearing files and internal ledger accounts without manual CSV exports.
* **X9.37 File Auto-Matching:** Check settlement files (Type 01/99 control records) are now parsed in-memory, auto-matching batch totals against transaction clearance logs.
* **Exception Queue:** Items flagged with out-of-balance totals are routed to a dedicated review panel under **Transactions > Exceptions** rather than rejecting the entire batch.
* **Performance Benchmark:** Ingestion latency reduced by 38% for batch archives exceeding 100,000 transaction records.

### 2. Fine-Grained API Token Scopes
Enhance security compliance by assigning least-privilege permissions to integration credentials.
* Replaced blanket admin service keys with explicit, resource-level scopes:
  * settlements:read
  * settlements:write
  * webhooks:configure
* Legacy integration keys will retain full access until January 31, 2027, but administrators are advised to rotate to scoped tokens immediately.

### 3. Extended Webhook Delivery Audit Logs
* Webhook failure logs now persist payload bodies and HTTP status responses for 90 days (increased from 14 days) to assist troubleshooting network drops with third-party processors.

---

## Breaking Changes

> Important: Review your file ingestion pipeline before deploying this update.

| Change | Affected Endpoints / Services | Required Action |
|---|---|---|
| Mandatory Record Timestamps | Inbound Batch Ingestion (/api/v2/batches/ingest) | Inbound X9.37 and NACHA file headers must now include an ISO-8601 creation timestamp (Field 04). Batches missing this field will return error code 422 Unprocessable Entity. |

---

## Bug Fixes & Improvements

### Core Processing
* **AP-4012:** Fixed an edge-case concurrency lock where simultaneous batch settlement submissions across multiple worker nodes triggered duplicate credit alerts.
* **AP-4089:** Resolved a memory leak in the transaction search worker during large wild-card customer account queries.

### User Interface & Reporting
* **AP-3950:** Corrected timezone localization discrepancy on the Daily Settlement Summary report where transactions near midnight UTC displayed on the subsequent business day.
* **AP-4102:** Updated the admin dashboard to persist custom column sorting preferences across active browser sessions.

---

## Upgrade & Deployment Instructions

### Prerequisites
1. Ensure your core database is running PostgreSQL 15.4 or higher.
2. Verify all active ingestion pipelines are paused prior to applying the database migration.

### Deployment Steps
1. Pull the release package:

    docker pull registry.apexpay.internal/gateway:4.2.0-b114

2. Execute the pre-deployment database migration script:

    ./bin/migrate --env=production --target=v4.2.0

3. Restart gateway services and monitor the health endpoint:

    curl -f texthttps://gateway.internal/health || exit 1

4. Resume batch ingestion queues and monitor error rates in the Exceptions Dashboard for 15 minutes.

---

## Related Information
* [X9.37 Batch Ingestion Specification](./task-guide.html)
* [API Scopes & Authentication Migration Guide](./index.html)
* [Disaster Recovery & Rollback Runbook](./index.html)
