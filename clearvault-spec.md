[← Back to Home](./index.html)

# System Specification: ClearVault Loan Event Integration & Webhook Dispatcher

**Document Identifier:** SPEC-CV-2026-08  
**Status:** Approved / Active Baseline  
**Audience:** Platform Architects, Integration Engineers, Loan Servicing Operations  
**Domain:** Commercial & Residential Lending Infrastructure  

---

## 1. Scope & Objective
This specification defines the functional architecture, message payloads, error-handling contracts, and audit requirements for the ClearVault Loan Event Integration Engine. The engine provides deterministic, asynchronous event dispatching for core loan lifecycle transitions (for example: rate-lock confirmations, escrow recalculations, and closing disclosure deliveries) to downstream servicing ledgers and secondary marketing adapters.

---

## 2. Architectural Principles & Guarantees
* **At-Least-Once Delivery:** All events are persisted to a durable message broker prior to dispatch. Downstream consumers must implement idempotent processing using the unique event_id.
* **Payload Immutability:** Event payloads capture the precise state of the loan record at event creation time. Modifications to upstream loan entities trigger distinct delta events.
* **Payload Encryption:** All sensitive borrower data (including PII, tax identifiers, and credit scores) is masked or encrypted using AES-256 field-level tokens before dispatch.

---

## 3. Data Contract: Loan Event Schema

### Standard Message Header
Every dispatched event adheres to the following top-level JSON structure:

    {
      "event_id": "evt_9f83b2a1-6c1e-4d22-9214-ef94119d8802",
      "event_type": "loan.escrow.analysis_completed",
      "schema_version": "2.1.0",
      "timestamp_utc": "2026-09-04T19:45:00Z",
      "institution_routing_id": "104000016",
      "payload": { ... },
      "audit_signature": "sha256:7e8bb24c..."
    }

### Field Definitions & Validation Constraints

| Attribute | Type | Nullable | Validation Rule & Constraints |
|---|---|---|---|
| event_id | String (UUIDv4) | No | Unique identifier generated at transaction commit. |
| event_type | String (Enum) | No | Dot-delimited domain action (for example: loan.origination.created, loan.escrow.analysis_completed). |
| schema_version | String | No | Semantic versioning format (MAJOR.MINOR.PATCH). |
| timestamp_utc | String (ISO-8601) | No | UTC timestamp formatted as YYYY-MM-DDTHH:mm:ssZ. |
| institution_routing_id | String | No | 9-digit ABA routing transit number of originating entity. |
| payload | Object | No | Domain-specific entity state (see Section 4). |
| audit_signature | String | No | HMAC SHA-256 hash computed using the shared tenant secret. |

---

## 4. Domain Payload Specification: Escrow Analysis

When event_type is set to `loan.escrow.analysis_completed`, the payload block must satisfy the following schema parameters:

| Parameter | Type | Required | Description & Business Rules |
|---|---|---|---|
| loan_account_number | String (10-12 chars) | Yes | Core loan servicing reference ID. |
| analysis_cycle_year | Integer (YYYY) | Yes | Current 12-month evaluation cycle. |
| annual_disbursements_projected | Decimal (12,2) | Yes | Projected disbursements for taxes and hazard insurance. |
| required_reserve_cushion | Decimal (12,2) | Yes | Maximum reserve cushion allowed under RESPA (1/6th total annual). |
| balance_variance_amount | Decimal (12,2) | Yes | Difference between current trial balance and required target. |
| adjustment_action | String (Enum) | Yes | Permitted values: SURPLUS_REFUND, SHORTAGE_SPREAD, SHORTAGE_LUMP_SUM, BALANCED. |
| refund_disbursement_id | String | Conditional | Required if adjustment_action is SURPLUS_REFUND and amount >= $50.00. |

---

## 5. Lifecycle State Transitions

The following state machine governs how loan event messages transition from publication through external acknowledgment:

    [LOAN ACTION COMMITTED]
              |
              v
       (PENDING_DISPATCH) ---> [DB PERSISTENCE ERROR] ---> (DEAD_LETTER_QUEUE)
              |
              v
        (DISPATCHING)
              |
        +-----+-----+
        |           |
     HTTP 200    HTTP 5xx / Timeout
        |           |
        v           v
    (DELIVERED)  (RETRY_SCHEDULED)
                    |
                    +--> Exceeded 5 Retries ---> (ESCALATED_MANUAL_REVIEW)

---

## 6. Error Handling & Retry Policies

### Delivery Retries
If an external endpoint returns a non-2xx status code or times out after 5,000 milliseconds, the dispatcher initiates exponential backoff with jitter:
* **Attempt 1:** 30 seconds
* **Attempt 2:** 2 minutes
* **Attempt 3:** 15 minutes
* **Attempt 4:** 1 hour
* **Attempt 5:** 6 hours

### Dead-Letter Queue (DLQ) Governance
Messages failing all five dispatch attempts transition to `ESCALATED_MANUAL_REVIEW`. 
* Operators must inspect the error payload via the ClearVault Admin Console.
* Payload reprocessing requires manual tenant authentication and emits an audited administrative replay event (`loan.event.replayed`).

---

## 7. Compliance & Regulatory Audit Requirements
To ensure compliance with Consumer Financial Protection Bureau (CFPB) record-keeping guidelines and SOC 2 Type II audit controls:
* **Retention Window:** Event records, delivery payloads, and transport status codes must remain immutable in cold storage for a minimum of 7 years post-closing.
* **Audit Trail Accessibility:** Every dispatched message must link to an internal operator ID or system process identifier to establish chain-of-custody.

---

## Related Portfolio Documents
* [Release Notes: ClearVault Loan Portal v3.4.0](./clearvault-release-notes.html)
* [Task Guide: Configuring Automated Escrow Analysis](./clearvault-task-guide.html)
* [System Architecture & Admin Guide: AI Media Catalog Engine](./app-architecture.html)
