[← Back to Home](./index.html)

# Release Notes: ClearVault Loan Portal v3.4.0

**Release Date:** September 2026  
**Build Identifier:** 3.4.0-rc4  
**Target Audience:** Loan Servicing Administrators, Underwriting Operations, Secondary Marketing Specialists  
**System Impact:** Requires a 10-minute scheduled maintenance window for database index re-indexing.

---

## Executive Summary
ClearVault Loan Portal v3.4.0 delivers an automated annual escrow analysis engine, dynamic TRID/TILA disclosure generation for mid-cycle loan modifications, and enhanced borrower co-signer data models. This release also introduces **one breaking schema change** requiring updates to automated underwriting interface (AUS) integration pipelines.

---

## What's New

### 1. Automated Annual Escrow Analysis Engine
Loan servicing departments can now process mass annual escrow reconciliations without third-party spreadsheet macros or manual balance entries.
* **Proactive Shortage/Surplus Calculations:** The system evaluates 12-month historical tax and hazard insurance disbursements against current escrow reserves, automatically projecting trial balance cushions based on RESPA limits (maximum 1/6th of total annual disbursements).
* **Automated Refund Disbursement Queues:** Net surpluses exceeding $50.00 are systematically aggregated into an accounts-payable batch queue for direct borrower refund check processing.
* **Notification Dispatch:** Integrated delivery triggers automatically post the finalized Escrow Analysis Disclosure statement to the borrower portal and flag print queues for physical mailing.

### 2. Real-Time TRID / TILA Disclosure Engine
Ensure ongoing compliance with federal disclosure timelines during active loan modifications:
* **Dynamic Change of Circumstance (COC) Tracking:** Rate-lock extensions, loan amount adjustments, or credit score shifts automatically evaluate whether a revised Loan Estimate (LE) or Closing Disclosure (CD) is legally required.
* **Audit Trail Checkpoints:** The portal timestamps disclosure receipt down to the second, matching the Consumer Financial Protection Bureau (CFPB) 3-day delivery rule.

### 3. Granular Underwriting Condition Queues
* Underwriters can now categorize credit stipulations into distinct functional buckets: **Prior to Document (PTD)**, **Prior to Funding (PTF)**, and **Post-Closing Audit**.
* Added document-level status toggles allowing loan processors to reject illegible borrower uploads with predefined reason tags (for example: Missing W-2 Pages 3-4, Incomplete Tax Schedules).

---

## Breaking Changes & Technical Considerations

> Important: Integration engineers must update AUS adapter payloads prior to upgrading to build 3.4.0-rc4.

| Component | Affected Area | Required Migration Action |
|---|---|---|
| Borrower Entity Schema | API: /api/v1/applications/{id}/borrowers | The single co_signer text field has been deprecated. Applications must now transmit secondary applicants inside the co_borrowers[] JSON array structure to support multi-party commercial and non-occupant co-signers. |
| County Tax Table | Database Table: ref_jurisdiction_tax | Added mandatory 5-digit FIPS_code column. Custom ingestion scripts populating tax assessment records without FIPS codes will fail schema validation. |

---

## Bug Fixes & Stability Enhancements

### Loan Servicing & Calculations
* **CV-2184:** Resolved an edge-case rounding calculation in odd-day per diem interest calculations on 365/360 commercial bridge notes.
* **CV-2201:** Corrected an issue where adjustable-rate mortgage (ARM) index reset jobs erroneously flagged fixed-rate secondary tranches.

### Portal UI & Document Management
* **CV-2149:** Fixed a race condition where simultaneous document uploads by both the borrower and loan officer caused file preview timeouts in the document viewer.
* **CV-2218:** Restored persistent sort order on the **Pipeline Status: Staged for Closing** view when toggling between branch offices.

---

## Deployment & Verification Runbook

### Prerequisites
1. Ensure the PostgreSQL reporting replica has synced all transaction logs from the primary node.
2. Confirm the message broker queue (loan event dispatcher) has fully cleared pending webhook events.

### Execution Steps
1. Place the loan processing frontend into maintenance mode:

    clearvault-ctl maintenance --enable --message "Applying scheduled update v3.4.0"

2. Execute database migrations and verify table schema indexes:

    ./bin/migrate-db --version=3.4.0 --run-preflight-checks

3. Start application services and inspect system health endpoints:

    curl -I texthttps://portal.clearvault.internal/api/v1/health/detailed

4. Disable maintenance mode and monitor background task queues:

    clearvault-ctl maintenance --disable

---

## Related Documentation
* [How to Configure Automated Escrow Analysis](./clearvault-task-guide.html)
* [Borrower Entity API v2 Migration Guide](./index.html)
* [Underwriting Condition Matrix & Runbook](./index.html)
