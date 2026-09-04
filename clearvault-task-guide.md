[← Back to Home](./index.html)

# How to Configure Automated Escrow Analysis & Surplus Distribution

**Target Audience:** Loan Servicing Specialists, Escrow Accounting Managers  
**Estimated Time:** 10–15 minutes  
**Architecture Pattern:** Every Page is Page One (EPPO) — Self-contained procedural topic  

---

## Purpose
This guide walks through configuring and executing an automated annual escrow analysis run in ClearVault Loan Portal v3.4.0. By completing this procedure, the system will calculate 12-month disbursement projections against historical property tax and insurance disbursements, determine compliance cushions per RESPA guidelines, and queue surplus refunds or shortage repayment schedules.

---

## Prerequisites
Before beginning this configuration, confirm you have:
* An active user account assigned the `escrow:admin` or `servicing:lead` role.
* Verified that county property tax tables and hazard insurance policy schedules for the target portfolio have been synced for the current tax assessment cycle.
* Verified that the target loan pool is in **Active Servicing** status (loans in active foreclosure, bankruptcy hold, or pending modification must be excluded).
* Access to the Accounts Payable (AP) clearing ledger for surplus check generation.

---

## Procedure

### Step 1: Select Portfolio Scope & Analysis Cycle
1. Log in to the ClearVault portal and navigate to **Servicing > Escrow Management > Analysis Profiles**.
2. Click **New Analysis Run**.
3. Define the loan selection criteria:
   * **Run Name:** Enter a descriptive label (for example, `2026-Q3-Midwest-Residential-Annual`).
   * **Analysis Period:** Select `Annual (12-Month Standard Projection)`.
   * **Loan Program:** Choose target asset classes (for example, `Conventional Fixed 30`, `FHA Prime`).
   * **Jurisdiction Filter:** Select the relevant states or county FIPS codes to limit evaluation to current tax reassessment regions.

---

### Step 2: Establish Calculation & Reserve Cushion Rules
Enforce Real Estate Settlement Procedures Act (RESPA) compliance limits on target reserves:

1. Click the **Calculation Rules** tab.
2. Under **Maximum Allowable Cushion**, verify the setting is toggled to `2 Months (1/6th Annual Disbursements)`.
3. Configure the **Shortage Treatment Rule**:
   * If shortage is less than 1 month of escrow payment: Select `Spread across 12-month billing schedule`.
   * If shortage exceeds 1 month of escrow payment: Select `Require lump-sum option with 12-month fallback`.
4. Configure the **Surplus Distribution Threshold**:
   * Set **Automated Refund Trigger** to `$50.00`.
   * *Note:* Net surpluses equal to or exceeding $50.00 will automatically route to the AP check queue. Surpluses under $50.00 will be credited toward the borrower's subsequent monthly escrow balance.

---

### Step 3: Run the Pre-Analysis Simulation (Dry Run)
Before committing financial changes to active ledgers, run an analytical trial to catch discrepancies:

1. In the top right corner, click **Execute Simulation**.
2. Monitor the progress bar until the status badge displays **Simulation Complete**.
3. Inspect the run summary metrics:
   * **Total Loans Evaluated:** Confirms the size of the target pool.
   * **Projected Shortages:** Counts accounts requiring monthly payment upward adjustments.
   * **Projected Surpluses:** Counts accounts generating refund payouts.
   * **Exceptions Flagged:** Accounts with missing tax bills or unapplied hazard adjustments.
4. If exceptions are greater than zero, click **Review Exceptions** to resolve missing parcel IDs or rate sheets before proceeding.

---

### Step 4: Finalize the Run and Generate Borrower Disclosures
Once all exceptions are resolved or bypassed:

1. Return to the run overview and click **Apply Final Analysis**.
2. Enter your secondary authorization credentials when prompted by the two-person rule dialog.
3. Select **Queue Disclosures**:
   * Generates the compliant **Annual Escrow Account Disclosure Statement** PDF for each borrower.
   * Dispatches notifications to the borrower portal document repository.
   * Queues paper statements for bulk print-and-mail distribution.

---

### Step 5: Export Surplus Batches to Accounts Payable
1. Navigate to **Escrow Management > Surplus Disbursement Queue**.
2. Filter by your current run name (`2026-Q3-Midwest-Residential-Annual`).
3. Click **Export AP Batch File**.
4. Confirm the export format matches your clearinghouse standard (for example, NACHA PPD Credit or NACHA Corporate AP).
5. Transmit the generated batch file to your central accounting system for refund check printing and direct ACH disbursement.

---

## Verification & Auditing
To verify that the analysis successfully took effect across the portfolio:
* Open an individual loan file from the processed batch via **Loans > Account Search**.
* Navigate to the **Accounting > Escrow Ledger** tab.
* Confirm that a transaction line titled `ANNUAL_ESCROW_ANALYSIS_COMPLETED` is recorded with the current system date.
* Verify that the borrower's **Next Due Monthly Payment** reflects the recalculation adjusted for shortage, surplus, or tax changes.

---

## Troubleshooting Common Errors

| Error Code / Flag | Root Cause | Remediation Procedure |
|---|---|---|
| `ERR_TAX_TABLE_OUTDATED` | County assessment records lack the current millage tax rate. | Go to **Admin > Tax Jurisdictions**, upload the current county rate table, and re-run the simulation. |
| `ERR_ESCROW_NEGATIVE_BALANCE` | Account had uncollected advances during the previous billing cycle. | Flag the loan for manual review under **Servicing > Recovery Queues**; negative balances cannot auto-calculate cushions. |
| `ERR_DISCLOSURE_RENDER_FAIL` | Missing borrower primary mailing address in profile. | Update borrower contact records in the application core and select **Regenerate Statement**. |

---

## Related Documentation
* [Release Notes: ClearVault Loan Portal v3.4.0](./clearvault-release-notes.html)
* [RESPA Section 10 Escrow Compliance Guidelines](./index.html)
* [Accounts Payable NACHA Export Configuration](./index.html)
