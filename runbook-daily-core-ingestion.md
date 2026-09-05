# Runbook: Daily Core Ingestion & Clearing Reconciliation

**Service:** `clearing-reconciliation-worker`  
**Target Environment:** Production  
**Execution Cadence:** Daily (Post-Clearing Window | 02:00 UTC)  
**Standard Execution Time:** ~12 minutes  
**Primary Escalation:** Core Ingestion SRE / Platform Ops (#ops-clearing-bridge)

---

## 1. Overview & Operational Scope

This runbook details standard verification, execution, and triage workflows for the automated settlement and clearing ingestion pipeline. It applies to end-of-day transaction sorting across high-volume clearing payloads, batch validation rules, and General Ledger (GL) staging queues.

---

## 2. Prerequisites & Access Controls

Verify the following credentials and terminal connectivity prior to manual intervention:

* [ ] Elevated production role via enterprise bastion host (`ssh-vault prod-ingest`)
* [ ] Read/Write access to the reconciliation pipeline namespace in Kubernetes
* [ ] Database read privileges on `core_ledger_db` and read privileges on S3 bucket `s3://clearing-archive-prod/`

---

## 3. Standard Execution Workflow

### Step 1: Pre-Execution Environment Health Check
Execute the health check script to confirm message queue depths are within acceptable operational limits:

```bash
kubectl exec -it -n core-banking deploy/reconciliation-engine -- ./healthcheck.sh --queue-depth
```

* **Pass Criteria:** Message lag < 500 records; worker node CPU utilization < 70%.
* **Fail Action:** If lag > 10,000 records, halt and refer to **Section 4: Rollback & Remediation**.

### Step 2: Trigger Batch Reconciliation
Initiate standard processing via the CLI utility:

```bash
recon-cli execute-batch --run-mode=strict --enforce-schema=v4.2 --notify-webhook
```

### Step 3: Verify Output Ledger Parity
Query the staging schema to confirm credit/debit balances evaluate to net zero:

```sql
SELECT 
    batch_id, 
    SUM(credit_amt) - SUM(debit_amt) AS variance, 
    status 
FROM staging_clearing_records 
WHERE execution_date = CURRENT_DATE 
GROUP BY batch_id, status;
```

* **Expected Output:** `variance = 0.00` and `status = 'STAGED'`.

---

## 4. Troubleshooting & Remediation

| Symptom / Alert | Root Cause | Immediate Action |
| :--- | :--- | :--- |
| `ERR_PAYLOAD_VALIDATION_FAILED` | Incompatible record format in source batch file. | 1. Isolate the offending record using `recon-cli validate --trace-id=<ID>`.<br>2. Move failed record to dead-letter queue (DLQ).<br>3. Re-run batch via standard retry flag. |
| `ERR_LEDGER_VARIANCE_MISMATCH` | Out-of-balance debits/credits detected during staging. | **DO NOT OVERRIDE.** Escalate immediately to Settlement Operations and notify the On-Call Product Consultant. |
| Worker Timeout / Pod Eviction | Node memory pressure during high-volume ingestion. | Scale replicas temporarily: `kubectl scale deploy/reconciliation-engine --replicas=6 -n core-banking`. |

---

## 5. Post-Execution & Run Sign-Off

1. Verify execution logs show status `COMPLETED_WITHOUT_WARNING`:
   ```bash
   kubectl logs -n core-banking -l app=clearing-worker --tail=50
   ```
2. Archive generated audit log artifacts to standard audit retention vault.
3. Update daily operations checklist in ServiceNow / Confluence with execution timestamp and operator ID.
