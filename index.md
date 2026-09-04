# Release Notes: ApexPay Gateway v4.2.0

**Release Date:** September 2026  
**Target Audience:** Platform Administrators & Integration Engineers  

---

## Overview
ApexPay Gateway v4.2.0 introduces automated transaction reconciliation for batch settlement files, expands OAuth2 token lifespan options, and resolves edge-case latency spikes during peak processing windows.

## What's New

### 1. Automated Batch Reconciliation (X9.37 Formats)
* **Real-Time Settlement Matching:** Ingestion pipelines now cross-reference inbound settlement batches against cleared ledger balances automatically.
* **Audit Trail Dashboards:** Operational teams can view mismatch flags directly in the admin portal under **Reporting > Exceptions**.

### 2. Security & Token Configuration
* **Configurable Expiration:** Added administrative controls to configure bearer token lifetimes from `15m` to `24h`.
* **Scoped Permissions:** API keys can now be scoped strictly to read-only reporting endpoints (`reporting:read`).

---

## Bug Fixes & Enhancements

| Component | Issue ID | Description | Impact |
|---|---|---|---|
| **Auth Service** | AP-1042 | Fixed race condition during simultaneous session refresh calls. | Low latency improvement |
| **Ingestion** | AP-1088 | Resolved parsing failures on non-standard carriage returns in legacy files. | Prevents silent batch drops |

---

## Upgrade Instructions
1. Navigate to the **Admin Settings > Updates** panel.
2. Verify existing database migrations are complete before applying build `4.2.0-b88`.
3. Clear server cache and monitor the `/health` endpoint for 60 seconds post-deployment.
