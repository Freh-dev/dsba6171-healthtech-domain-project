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

The microcosm contains **six structured datasets** stored in `data/structured/raw/` that follow a normalized healthcare claims data model.

---

### `patient_accounts.csv` (Entity)

Member/patient enrollment and insurance plan information. One row represents a unique member enrollment record.

| Example Fields | Description |
|----------------|-------------|
| `member_id` | Unique member identifier (Primary Key) |
| `first_name` | Member's first name |
| `last_name` | Member's last name |
| `date_of_birth` | Date of birth |
| `gender` | M, F, Other |
| `address_line1` | Street address |
| `address_line2` | Apartment/suite (optional) |
| `city` | City |
| `state` | State code |
| `zip_code` | ZIP code |
| `phone_number` | Contact number |
| `email` | Email address (optional) |
| `plan_id` | References insurance_plans (Foreign Key) |
| `effective_date` | Coverage start date |
| `termination_date` | Coverage end date (NULL if active) |
| `primary_provider_id` | References providers (Foreign Key) |

---

### `providers.csv` (Entity)

Healthcare provider and facility information. One row represents a unique provider or facility.

| Example Fields | Description |
|----------------|-------------|
| `provider_id` | Unique provider identifier (Primary Key) |
| `provider_name` | Provider/facility name |
| `npi_number` | National Provider Identifier |
| `specialty` | Medical specialty |
| `tax_id` | Tax identification number |
| `address_line1` | Street address |
| `address_line2` | Suite/floor (optional) |
| `city` | City |
| `state` | State code |
| `zip_code` | ZIP code |
| `phone_number` | Contact number |
| `contract_status` | Active, Terminated, Pending |
| `network_tier` | In-Network, Out-of-Network, Preferred |

---

### `insurance_plans.csv` (Entity)

Insurance plan coverage and benefit information. One row represents a unique insurance plan.

| Example Fields | Description |
|----------------|-------------|
| `plan_id` | Unique plan identifier (Primary Key) |
| `plan_name` | Plan name (Gold PPO, Silver HMO) |
| `plan_type` | PPO, HMO, EPO, HDHP |
| `coverage_region` | Geographic coverage area |
| `deductible_amount` | Annual deductible |
| `out_of_pocket_max` | Maximum out-of-pocket |
| `copay_primary` | Copay for primary care visit |
| `copay_specialist` | Copay for specialist visit |
| `coinsurance_rate` | Coinsurance percentage |
| `effective_date` | Plan effective date |
| `termination_date` | Plan termination date (NULL if active) |

---

### `procedure_catalog.csv` (Entity)

Procedure and diagnosis reference information. One row represents a unique procedure code.

| Example Fields | Description |
|----------------|-------------|
| `procedure_code` | CPT/HCPCS code (Primary Key) |
| `procedure_description` | Description of procedure |
| `procedure_category` | Surgery, Eval & Management, Radiology, etc. |
| `billing_modifier` | Modifier code (optional) |
| `allowed_amount` | Typical allowed amount |
| `coverage_policy_id` | Links to knowledge corpus document (Foreign Key) |
| `is_active` | Is procedure currently active? (TRUE/FALSE) |

---

### `claims_ledger.csv` (Transaction - Header)

Claim-level header information. One row represents a unique claim submission.

| Example Fields | Description |
|----------------|-------------|
| `claim_id` | Unique claim identifier (Primary Key) |
| `member_id` | References patient_accounts (Foreign Key) |
| `provider_id` | References providers (Foreign Key) |
| `plan_id` | References insurance_plans (Foreign Key) |
| `claim_received_date` | Date claim was received |
| `claim_service_start_date` | First date of service |
| `claim_service_end_date` | Last date of service |
| `claim_status` | Submitted, Pending, Denied, Paid |
| `total_billed_amount` | Total billed amount |
| `total_allowed_amount` | Total allowed amount |
| `total_paid_amount` | Total paid amount |
| `diagnosis_code_primary` | Primary ICD diagnosis code |
| `diagnosis_code_secondary` | Secondary ICD diagnosis code (optional) |

---

### `claim_line_items.csv` (Transaction - Detail)

Claim line-level detail information. One row represents a single line item within a claim.

| Example Fields | Description |
|----------------|-------------|
| `claim_line_id` | Unique line item identifier (Primary Key) |
| `claim_id` | References claims_ledger (Foreign Key) |
| `line_number` | Line number within claim |
| `procedure_code` | References procedure_catalog (Foreign Key) |
| `service_date` | Date of service for this line |
| `billed_amount` | Billed amount for this line |
| `allowed_amount` | Allowed amount for this line |
| `paid_amount` | Paid amount for this line |
| `units` | Number of units/procedures |
| `modifier_code` | CPT modifier (optional) |
| `denial_reason_code` | Reason code if denied (optional) |

---

### Data Summary

| File | Type | Primary Key | Foreign Keys | Time Fields |
|------|------|-------------|--------------|-------------|
| `patient_accounts.csv` | Entity | `member_id` | `plan_id`, `primary_provider_id` | `effective_date`, `termination_date` |
| `providers.csv` | Entity | `provider_id` | - | - |
| `insurance_plans.csv` | Entity | `plan_id` | - | `effective_date`, `termination_date` |
| `procedure_catalog.csv` | Entity | `procedure_code` | `coverage_policy_id` | - |
| `claims_ledger.csv` | Transaction | `claim_id` | `member_id`, `provider_id`, `plan_id` | `claim_received_date`, `claim_service_start_date`, `claim_service_end_date` |
| `claim_line_items.csv` | Transaction | `claim_line_id` | `claim_id`, `procedure_code` | `service_date` |

---

### Relationship Summary

| Relationship | From | To | Cardinality |
|--------------|------|-----|-------------|
| Patient → Plan | `patient_accounts.plan_id` | `insurance_plans.plan_id` | Many-to-One |
| Patient → Provider | `patient_accounts.primary_provider_id` | `providers.provider_id` | Many-to-One |
| Claim → Patient | `claims_ledger.member_id` | `patient_accounts.member_id` | Many-to-One |
| Claim → Provider | `claims_ledger.provider_id` | `providers.provider_id` | Many-to-One |
| Claim → Plan | `claims_ledger.plan_id` | `insurance_plans.plan_id` | Many-to-One |
| Line → Claim | `claim_line_items.claim_id` | `claims_ledger.claim_id` | Many-to-One |
| Line → Procedure | `claim_line_items.procedure_code` | `procedure_catalog.procedure_code` | Many-to-One |

## 7. Structured Data Relationships

### Entity Relationship Diagram (ERD)
