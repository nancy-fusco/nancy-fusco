[← Back to Home](./index.html)

# How to Configure Automated X9.37 Batch Ingestion

**Target Audience:** Enterprise Integration Engineers, Core Banking Administrators  
**Estimated Time:** 10–15 minutes  
**Architecture Pattern:** Every Page is Page One (EPPO) — Self-contained procedural topic

---

## Purpose
This guide walks through configuring an automated ingestion pipeline for image cash letter (ICL / X9.37) files using ApexPay Gateway. By the end of this task, inbound batch settlement files deposited via secure transport will be parsed, validated against file-level control records, and automatically posted to settlement ledgers.

---

## Prerequisites
Before beginning this configuration, ensure you have:
* An active administrator account assigned the `settlements:write` and `pipelines:configure` permissions.
* Target SFTP or S3 bucket read credentials where clearing institutions deposit `.x937` or `.bin` archives.
* The 9-digit Federal Reserve Routing Transit Number (RTN) for your originating institution.
* ApexPay Gateway version 4.2.0 or higher deployed.

---

## Procedure

### Step 1: Define the Ingestion Pipeline Source
1. Log in to the ApexPay Admin Console and navigate to **Settings > Ingestion Pipelines**.
2. Select **Add Ingestion Profile** and choose **X9.37 / Image Cash Letter** from the format dropdown.
3. Configure the source transport parameters:
   * **Profile Name:** Enter a unique identifier (for example, `Fed-Clearing-Primary-01`).
   * **Transport Protocol:** Select `Secure S3 Bucket` or `SFTP External`.
   * **Directory Path:** Enter the polling directory path (for example, `/incoming/settlements/`).
   * **Polling Interval:** Set to `5m` (5 minutes) for standard automated clearing cycles.

---

### Step 2: Set Header & Control Record Validation Rules
To prevent partial ledger posting and detect corrupted check archives, enforce strict control record matching:

1. Under the **Control Record Schema** tab, set **Type 01 Header Validation** to `Enforce Strict Matching`.
2. Enter your originating routing number into **Origin Routing Identifier (Field 04)**.
3. Check the box labeled **Require Field 04 Timestamp (ISO-8601)** to comply with Gateway v4.2.0 requirements.
4. Set **Type 99 File Control Verification** to `Auto-Balance Check`:
   * The parser calculates the sum of all Type 25 (Check Detail) records and compares it against the total amount defined in Record Type 99 (Field 04).
   * If discrepancy exceeds $0.00, the batch is moved directly to the Exceptions Queue.

---

### Step 3: Configure Ledger Settlement Mapping
Map inbound transactions to internal accounts before triggering the downstream clearing workflow:

1. Click **Mapping & General Ledger**.
2. Under **Transit Credit Account**, select the default settlement ledger from your chart of accounts.
3. Under **Return Routing Account**, specify the reserve account for handling NSF and return items.
4. Set the **Duplicate Item Check** threshold to `90 Days`.

---

### Step 4: Verify the Configuration with a Test Payload
Before activating the pipeline in production, run a dry-run validation using a synthetic check batch:

1. In the top right corner of the profile view, click **Run Diagnostic Ingestion**.
2. Upload a sanitized sample archive (for example, `sample_clearing_test.x937`).
3. Observe the validation response log:

    POST /api/v2/pipelines/Fed-Clearing-Primary-01/validate
    Status: 200 OK
    Records_Parsed: 1,420
    Control_Type01: VALID (Timestamp: 2026-09-04T15:10:00Z)
    Control_Type99: BALANCED (Total: $421,890.50)
    Exceptions: 0

4. Confirm that all four status checks return green badges without control record mismatches.

---

### Step 5: Activate the Pipeline
1. Toggle the **Pipeline Status** switch at the top of the screen to **Active**.
2. Click **Save & Apply Changes**.
3. Confirm activation when prompted. The gateway will initiate automated polling on the configured cycle.

---

## Verification & Monitoring
Verify your pipeline is healthy after activation:
* Navigate to **Transactions > Real-Time Monitor**.
* Confirm the pipeline state displays as **Healthy / Polling**.
* When the first automated file arrives, verify that a corresponding ledger entry appears in **Reporting > Settlement Summaries** within 60 seconds of file drop.

---

## Troubleshooting Common Errors

| Error Code | Root Cause | Resolution |
|---|---|---|
| `ERR_X9_422_TIMESTAMP_MISSING` | The clearing party sent a file without an ISO-8601 timestamp in Field 04. | Contact the upstream provider or temporarily enable legacy relaxed mode under **Settings > Compliance Overrides**. |
| `ERR_X9_BAL_MISMATCH` | The record sum in Record Type 25 does not match the master control total in Record Type 99. | Batch is held in **Transactions > Exceptions**. Review mismatched check amounts manually. |
| `ERR_AUTH_CREDENTIAL_REJECTED` | The storage access key lacks read permissions on the polling bucket. | Verify AWS IAM or SFTP read/write policy attachments. |

---

## Related Documentation
* [Release Notes: ApexPay Gateway v4.2.0](./release-notes.html)
* [API Scopes & Authentication Migration Guide](./index.html)
* [Exception Resolution Runbook](./index.html)
