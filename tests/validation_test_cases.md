# Architecture Validation Test Cases

**Team:** Team 2 (HealthTech)
**Purpose:** Explicit validation criteria to prove that the remediation controls defined in MA2 actually work before we pass data to the AI.

## QI-001: Policy Authority Gate
* **Control Rule:** The AI must never retrieve a policy document unless `authority_status = 'Current'` or `'Approved'`.
* **Test Scenario:** Submit a retrieval query for "Prior Authorization rules".
* **Expected Pass:** The system successfully retrieves `HLT-POL-001` (Current) but completely blocks `HLT-PROD-01` (Draft) from reaching the AI context.
* **Expected Fail:** `HLT-PROD-01` appears in the LLM context window.

## QI-002: Point-in-Time Retrieval & Escalation
* **Control Rule:** Policy retrieved must be active on the claim's `service_date`. If no valid policy exists, the claim must be escalated.
* **Test Scenario A (Valid Match):** Evaluate a claim with `service_date = '2024-06-15'`.
* **Expected Pass A:** System retrieves `HLT-POL-002` (the 2024 booklet) and explicitly rejects `HLT-POL-001` (the 2025 booklet).
* **Test Scenario B (No Match):** Evaluate a claim with `service_date = '2022-01-01'` (before any policies were effective).
* **Expected Pass B:** System returns a `NO_VALID_POLICY` flag and routes the claim to the Manual Review Queue, preventing the AI from guessing.

## QI-003: Pipeline Idempotency
* **Control Rule:** Re-running the ingestion pipeline must not duplicate records in the database.
* **Test Scenario:** Execute the ingestion script twice on the exact same `claims_ledger.csv` file.
* **Expected Pass:** The DuckDB table row count remains exactly the same after the second run. No new `claim_id` duplicates are created.

## QI-003b: Business-Level Duplicate Detection
* **Control Rule:** Resubmitted claims (different `claim_id`, but same business details) must be quarantined before the AI sees them.
* **Test Scenario:** Ingest a test row matching an existing claim's `member_id`, `provider_id`, `procedure_code`, `service_date`, and `modifier_code`, but with a newly generated `claim_id`.
* **Expected Pass:** The system flags the row with `dq_flag = 'POSSIBLE_DUPLICATE'` and moves it to the Quarantine table, keeping the AI-ready table clean.