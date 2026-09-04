[← Back to Home](./index.html)

# System Architecture & Admin Guide: Dynamic Catalog & AI Recommendation Engine

**Document Type:** System Specification & Administrative User Guide  
**Stack:** Google AI Studio, Gemini API, Structured Ingestion, Markdown  
**Architecture Pattern:** Context-Constrained Ingestion & Deterministic Filtering  

---

## Executive Summary
This document provides the technical architecture and administrative runbook for the Dynamic Media Catalog Application. Built on Google AI Studio, the application ingests structured catalog data from comma-separated values (.csv) archives, maps tabular attributes into an in-memory knowledge store, and executes prompt-directed probabilistic selections constrained to verified inventory.

The architecture solves two key challenges common to generative catalog systems:
* **Hallucination Elimination:** The model is strictly bound to ingested records, preventing recommendations for unowned titles.
* **Deterministic Taxonomy Alignment:** Free-text user inputs are normalized against standard genre hierarchies before candidate selection.

---

## System Architecture & Information Model

### 1. Data Contract & Ingestion Schema
The ingestion pipeline requires a normalized tabular schema. Files submitted with missing column headers or non-standard date formats are rejected at the parsing boundary.

| Field Name | Type | Constraint | Description |
|---|---|---|---|
| item_id | String (UUID/Alphanumeric) | Required, Unique | Primary catalog key used for inventory indexing. |
| title | String | Required | Full release title as cataloged in physical inventory. |
| primary_genre | String (Enum) | Required | Primary classification (Action, Comedy, Drama, Sci-Fi, Thriller). |
| sub_genre | String | Optional | Secondary taxonomy tag for nuanced search filtering. |
| release_year | Integer (YYYY) | Required | Year of original distribution. |
| runtime_min | Integer | Optional | Total duration in minutes (used for threshold queries). |
| physical_location | String | Required | Media storage shelf or bin identifier (for example, Shelf-B3). |

---

### 2. Prompt Architecture & Constraints
To guarantee reliable title selection, the system instructions inside Google AI Studio operate under strict ontological and behavioral boundaries:

* **Role Definition:** Act as an automated inventory selector operating exclusively over the provided catalog table.
* **Grounding Rule:** You must never recommend, infer, or mention any title that does not appear in the active dataset.
* **Taxonomy Normalization:** When a user specifies an informal genre (for example, "funny movies" or "scary"), map the intent to the closest enumerated primary_genre (Comedy or Horror).
* **Selection Logic:** 
  1. Filter the dataset by the identified genre tag.
  2. If multiple matching records exist, apply a random seed selection across the filtered subset.
  3. Output the selected record alongside its physical location identifier.
  4. If zero titles match, return an explicit out-of-stock notification with nearest available genres.

---

## Administrator Configuration Guide

### Procedure 1: Preparing & Formatting the Catalog File
1. Open your inventory export spreadsheet.
2. Confirm the top row matches the seven required schema headers: item_id, title, primary_genre, sub_genre, release_year, runtime_min, physical_location.
3. Validate that all genre values match standard taxonomy terms to avoid mapping mismatches.
4. Export the file as UTF-8 encoded CSV.

---

### Procedure 2: Ingesting Data into the Application
1. Access the Google AI Studio console and open the **Catalog Manager Workspace**.
2. In the **System Context & Ingestion** panel, select **Upload Dataset**.
3. Choose your prepared CSV file.
4. Verify the file preview in the ingestion monitor:
   * Total records parsed matches the row count.
   * Delimiters (commas) render into proper column columns without orphaned fields.
5. Click **Confirm Ingestion**. The system binds the parsed tabular dataset to the active model context.

---

### Procedure 3: Running Diagnostic Selections
Before opening the catalog tool to general users, verify that the automated recommendation engine is functioning deterministically:

1. In the active prompt interface, enter a standard request:

    Recommend an Action title from the collection.

2. Verify that the response contains:
   * Exactly one title confirmed to be in the uploaded dataset.
   * The release year and runtime matching the source row.
   * The correct physical location tag (for example, Shelf-A2).

3. Execute an edge-case test with a non-existent genre:

    Recommend a Western title.

4. Confirm that the system returns the standard out-of-stock message rather than inventing a non-existent movie title.

---

## Error Handling & Troubleshooting

| Condition | Root Cause | Administrative Remedy |
|---|---|---|
| Model suggests unowned movie | System instructions corrupted or context overflow. | Reapply system constraint rules; confirm dataset size does not exceed context boundary. |
| Ingestion rejected at upload | CSV contains unescaped quotes or non-UTF-8 characters. | Re-export file ensuring standard RFC 4180 CSV compliance and UTF-8 encoding. |
| Inaccurate genre mapping | User entered niche keyword not covered in sub_genre. | Add the term to the sub_genre column or update the prompt's synonym mapping table. |

---

## Related Portfolio Samples
* [Case Study: AI-Accelerated Legacy Transformation](./content-architecture.html)
* [Enterprise Release Notes: ApexPay Gateway](./release-notes.html)
* [How to Configure Automated X9.37 Batch Ingestion](./task-guide.html)
