# HealthTech Domain Project — Medical Claims & Payer Auditing Microcosm

This repository contains the **HealthTech Domain Data & Knowledge Microcosm** developed for **Assignment 1 — Domain Data & Knowledge Microcosm Blueprint** in **DSBA 6171 — Data Engineering for AI**.

The project models a realistic medical claims review and payer auditing environment using structured healthcare data, source documents, metadata, and domain relationships. The microcosm is designed to support data engineering, knowledge retrieval, AI-assisted decision support, explainability, and governance in subsequent course assignments.

---

## 1. Assigned Domain

**HealthTech — Medical Claims & Payer Auditing**

The project focuses on the intersection of healthcare claims processing, insurance coverage, medical procedures, payer policies, and regulatory requirements.

---

## 2. Team Information

### Team Members & Primary Roles

| Team Member  | Primary Role |
|--------------|--------------|
| Elijah Jones | Data & Ingestion Lead |
| Frehiwot Haile| Database & Analytics Lead |
| Daniel Nunes | Quality & Governance Lead |
| Dzmitry Prybysh | Knowledge & Retrieval Lead |
| Ketan Kumar Behera | Domain & Strategy Support |

*Roles indicate primary ownership; all team members contribute to end-to-end architecture decisions.*

---

## 3. Business Scenario

This microcosm represents the workflow of **medical claims review and payer auditing** within a mid-sized health insurance organization.

Healthcare payers receive a large volume of claims submitted by providers. Each claim must be evaluated against:
- Member eligibility
- Plan benefits and coverage rules
- Procedure and diagnosis codes
- Medical necessity requirements
- Prior authorization rules
- Applicable regulatory policies

### Key Challenges

| Challenge | Description |
|-----------|-------------|
| High claim volume | Limited manual review capacity |
| Complex coverage rules | Frequently changing plan benefits |
| Regulatory variation | Different requirements across states |
| Inconsistent decisions | Policy interpretation differences |
| FWA risks | Fraud, waste, and abuse detection |
| Explainability | Need transparent claim decisions |

The microcosm provides a controlled environment for exploring how data engineering and AI can support more accurate, efficient, consistent, and explainable claim review.

---

## 4. Primary Decision Question

> **"Given the member's plan coverage, the submitted procedure code, and current payer policies, should this claim be approved, denied, or escalated for clinical review?"**

This decision question connects structured claim data with domain knowledge contained in benefits documents, clinical guidelines, prior-authorization policies, and regulatory materials.

---

## 5. Supporting Questions

| # | Supporting Question |
|---|---------------------|
| 1 | Is the member eligible for coverage on the date of service? |
| 2 | Is the submitted procedure covered under the member's plan? |
| 3 | Does the claim satisfy medical necessity criteria? |
| 4 | Does the procedure require prior authorization, and was it obtained? |
| 5 | Are the procedure and diagnosis codes valid and appropriately related? |
| 6 | Are there jurisdiction-specific rules affecting this claim? |
| 7 | Does the claim contain fraud, waste, or abuse indicators? |
| 8 | Does the claim require escalation for clinical or manual review? |

---

## 6. Structured Data

The microcosm contains three primary structured datasets stored in `data/structured/raw/`.

### `member_accounts.csv` (Entity)

Member enrollment and insurance plan information.

| Example Fields | Description |
|----------------|-------------|
| `member_id` | Unique member identifier (Primary Key) |
| `plan_code` | Insurance plan code (e.g., GOLD-PPO) |
| `region` | Member's geographic region |
| `eligibility_start` | Coverage start date |
| `eligibility_end` | Coverage end date |

### `procedure_catalog.csv` (Entity)

Procedure and diagnosis reference information.

| Example Fields | Description |
|----------------|-------------|
| `procedure_code` | Procedure code (Primary Key) |
| `procedure_description` | Description of procedure |
| `diagnosis_category` | Diagnosis category classification |
| `prior_authorization_required` | Yes/No flag |
| `review_flag` | Routine, High Cost, Requires Prior Auth |

### `claims_ledger.csv` (Transaction)

Claim-level transactional information.

| Example Fields | Description |
|----------------|-------------|
| `claim_id` | Unique claim identifier (Primary Key) |
| `member_id` | References member_accounts (Foreign Key) |
| `procedure_code` | References procedure_catalog (Foreign Key) |
| `service_date` | Date service was performed |
| `billed_amount` | Amount billed by provider |
| `allowed_amount` | Allowed amount by payer |
| `decision_status` | Approved, Denied, Pending Review |

---

## 7. Structured Data Relationships

### Entity Relationship Diagram (ERD)
