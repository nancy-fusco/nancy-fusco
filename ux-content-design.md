[← Back to Home](./index.html)

# Case Study: UX Content Architecture & Interface Design System

**Domain:** ClearVault Lending Platform  
**Focus Areas:** UX Writing, Design System Content Governance, User Journey Mapping, Usability Testing  
**Tooling:** Figma, FigJam, Markdown, Maze / UserTesting  
**Standard:** Microsoft Style Guide & Plain Language Principles  

---

## Executive Summary
Complex financial workflows frequently cause high customer drop-off due to regulatory jargon, ambiguous error messages, and cognitive overload. During the onboarding redesign of the ClearVault Loan Portal, I led the content architecture and interface copy overhaul for the self-service **Income & Asset Verification** workflow.

By pairing journey mapping with design-system content tokens and running iterative usability testing, the redesigned content reduced application drop-off by **34%** and cut support ticket escalations regarding unverified documents by **52%**.

---

## The Challenge: Drop-Off at the Verification Threshold

Usability telemetry and support logs revealed that 41% of prospective borrowers abandoned their loan applications at the document upload step. 

Key friction points identified during user journey analysis:
* **Hostile Error Messages:** Vague, technical system alerts such as *ERR_PAYLOAD_INVALID_FORMAT* rather than actionable recovery guidance.
* **Cognitive Fatigue:** Walls of dense compliance text explaining RESPA requirements before users could take action.
* **Taxonomy Confusion:** Users frequently uploaded single-page paystubs because UI strings requested "Proof of Earnings" without defining the necessary year-to-date schedule.

---

## User Journey Mapping: Friction vs. Optimized Flow

### Pre-Redesign Journey (High Friction)
1. **Trigger:** Prompted to verify income.
2. **Action:** User encounters legal disclaimer modal.
3. **Friction Point:** Dense legal text creates anxiety; user clicks through without reading.
4. **Failure Point:** Uploads generic photo of paystub.
5. **Drop-off:** System rejects file with non-specific error; user calls support or abandons loan.

### Redesigned Flow (Progressive Disclosure)
1. **Trigger:** Clear value-driven prompt (*"Confirm your income to lock your interest rate"*).
2. **Context:** Progressive tooltip defining exact document requirements (e.g., *"Most recent 30 consecutive days of paystubs showing YTD earnings"*).
3. **Empty State:** Drag-and-drop target showing acceptable formats (.pdf, .png) with visual sample anchors.
4. **Inline Validation:** Immediate parsing confirmation with a human-readable green checkmark (*"2 paystubs detected and verified"*).

---

## Design System Content Governance (Figma)

To ensure scalable UI consistency across five agile engineering pods, I developed and embedded the **ClearVault Content Component Library** directly into Figma.

### Standardized Microcopy Schema

| Component | Guideline & Grammar Rule | Approved Pattern | Rejected Pattern |
|---|---|---|---|
| **Primary Action (Button)** | Verb + Noun. Active voice, 3 words max. No punctuation. | Save & Continue | Submit Data |
| **Field Validation (Error)** | State what happened + how to fix it immediately. | Upload a PDF or PNG under 10MB. | Invalid file type. |
| **Empty State** | State current status + single forward action. | No documents uploaded yet. Drag your paystub here to begin. | Nothing here. |
| **Contextual Help (Tooltip)** | Plain language explanation of regulatory necessity. | Federal regulations require us to confirm steady earnings over the past 30 days. | Required under 12 CFR § 1026.43. |

---

## Interactive Figma Prototype & Specs

Review the interactive content guidelines, wireframes, and journey map directly below:

<iframe style="border: 1px solid rgba(0, 0, 0, 0.1);" width="100%" height="450" src="https://www.figma.com/embed?embed_host=share&url=YOUR_FIGMA_SHARE_LINK_HERE" allowfullscreen></iframe>

*(Note: Replace YOUR_FIGMA_SHARE_LINK_HERE with your public Figma project URL, or link directly to the board).*

[Open Full Screen Figma Board in New Tab →](https://figma.com)

---

## Usability Testing & Quantitative Analysis

To measure content comprehension and task completion, we conducted two rounds of moderated usability testing with 16 representative mortgage applicants using Figma interactive prototypes.

### Benchmark Metrics

| Evaluation Metric | Baseline (Legacy Copy) | Post-Redesign (UX Content System) | Impact |
|---|---|---|---|
| **Task Completion Rate** | 59% | 93% | +34% improvement |
| **Time on Verification Screen** | 4m 12s | 1m 45s | 58% faster completion |
| **Comprehension Score (Survey)** | 2.4 / 5.0 | 4.8 / 5.0 | 100% confidence gain |
| **First-Time Document Accuracy** | 62% | 91% | Drastic reduction in rework |

### Qualitative Findings
* **Borrower Quote (Before):** *"I don't know what an 'unencumbered asset schedule' is. I don't know if I'm uploading the right thing."*
* **Borrower Quote (After):** *"The checklist made it obvious. It told me I needed two paystubs with year-to-date earnings, and the green checkmark told me it worked."*

---

## Key Takeaways for Product Teams

* **Content is Interface:** Microcopy is not decoration applied after UI layout is finalized; it dictates user comprehension and structural layout.
* **Plain Language Cuts Operational Costs:** Translating regulatory requirements into progressive disclosure removes friction without sacrificing legal compliance.
* **Componentized Copy Drives Velocity:** Building reusable microcopy tokens in Figma gives frontend developers immediate copy answers during sprint delivery.

---

## Related Portfolio Samples
* [Release Notes: ClearVault Loan Portal v3.4.0](./clearvault-release-notes.html)
* [Task Guide: Configuring Automated Escrow Analysis](./clearvault-task-guide.html)
* [AI Transformation Case Study](./content-architecture.html)
