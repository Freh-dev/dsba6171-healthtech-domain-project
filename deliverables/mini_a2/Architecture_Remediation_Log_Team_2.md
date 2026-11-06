# Architecture Remediation Log – Team 2

## Purpose
This remediation log records the architectural corrections required to make the healthtech microcosm safe for AI-assisted claims review and payer auditing. It closes the gap between the domain design and the governance conditions required for trustworthy retrieval, decision support, and explainability.

The repository already documents the core domain context in [README.md](../README.md), the controlled quality issues in [metadata/quality_issues_log.csv](../metadata/quality_issues_log.csv), and the governance metadata in [metadata/document_metadata.csv](../metadata/document_metadata.csv). This log translates those issues into explicit remediation actions and validation controls.

---

## 1. Remediation Summary

| Issue ID | Architecture Layer | Root Cause | Remediation Action | Validation / Control | Status |
|----------|-------------------|------------|-------------------|--------------------|--------|
| QI-001 | Knowledge corpus / governance metadata | Draft policy content exists in the corpus without confirmed authority or linked retrieval path | Block draft or non-authoritative source documents from AI indexing and retrieval; require explicit authority_status check before inclusion | Document eligibility gate; only Current or Approved documents can be indexed | Planned / ready for implementation |
| QI-002 | Knowledge corpus / document lifecycle | Superseded policy documents can be selected by generic plan attributes without point-in-time matching | Enforce temporal retrieval logic using effective_date and termination_date against claim service_date; never match only on plan type or region | Temporal validation at retrieval time; quarantine no-match and multi-match cases | Planned / ready for implementation |
| QI-003 | Structured data / ingestion | Duplicate or near-duplicate claims are treated as independent transactions | Add deduplication or idempotency checks before raw data enters the claim ledger; quarantine resubmissions before analytics or AI scoring | Hash-based dedupe, duplicate detection, and quarantine rule | Planned / ready for implementation |

---

## 2. Detailed Remediation Actions

### QI-001: Draft document authority check
The current repository identifies a draft document, HLT-PROD-01, in [metadata/document_metadata.csv](../metadata/document_metadata.csv). This is not yet authoritative and has no confirmed retrieval linkage in [metadata/structured_to_knowledge_linkages.csv](../metadata/structured_to_knowledge_linkages.csv).

Remediation:
- Require authority_status in {Current, Approved} before any document is eligible for retrieval or indexing.
- Treat Draft, Proposed, and other non-authoritative states as quarantine candidates.
- Require a documented retrieval path before a knowledge item can be used in claim decisions.

Architectural impact:
- Prevents AI systems from surfacing unapproved policy as if it were a binding rule.
- Preserves audit defensibility when claims are approved, denied, or escalated.

---

### QI-002: Point-in-time retrieval logic
The microcosm explicitly includes the case where a superseded 2024 benefits document is still retrievable by plan attributes, even though it should only apply within its effective period. This is documented in [metadata/quality_issues_log.csv](../metadata/quality_issues_log.csv) and in the business risk section of [README.md](../README.md).

Remediation:
- Match policies to the claim using service_date, effective_date, termination_date, and jurisdiction.
- Restrict document retrieval to documents that are valid as of the claim date.
- If a document is superseded, do not use it for current-year coverage decisions unless the claim date falls inside its effective window.

Architectural impact:
- Prevents stale benefit rules from influencing present-day claim decisions.
- Creates a defensible, time-aware knowledge retrieval layer for claims adjudication.

---

### QI-003: Duplicate transaction handling
The repository includes a near-duplicate claim scenario in [data/structured/raw/claims_ledger.csv](../data/structured/raw/claims_ledger.csv), as described in [metadata/quality_issues_log.csv](../metadata/quality_issues_log.csv).

Remediation:
- Add ingestion-stage deduplication logic using a deterministic idempotency key built from the core business event.
- Quarantine resubmitted claims before they pass into the analytical or AI layer.
- Preserve the original claim_id and track resubmission metadata for audit traceability.

Architectural impact:
- Avoids inflated claim counts and incorrect financial totals.
- Protects fraud, waste, and abuse detection models from duplicate-event bias.

---

## 3. Repository Evidence Supporting the Remediation Plan

- Business scenario and AI decision framing: [README.md](../README.md)
- Controlled issue catalog: [metadata/quality_issues_log.csv](../metadata/quality_issues_log.csv)
- Document lifecycle and authority status: [metadata/document_metadata.csv](../metadata/document_metadata.csv)
- Knowledge routing and policy linkage: [metadata/structured_to_knowledge_linkages.csv](../metadata/structured_to_knowledge_linkages.csv)
- Semantic controls: [contracts/semantic/claim_line_items_quality_controls_v1.json](../contracts/semantic/claim_line_items_quality_controls_v1.json)
- Risk framing: [README.md](../README.md)

---

## 4. Recommended Future Controls

1. Authority gate for knowledge documents
   - Only Current or Approved documents may enter policy retrieval.

2. Temporal retrieval enforcement
   - Service date must be used to select the valid policy version.

3. Ingestion deduplication and idempotency
   - Duplicate claims are quarantined before entering the analytics domain.

4. Explainability and auditability
   - Every decision should be traceable to the policy version, effective date, and claim context used.

---

## 5. Conclusion
The repo already demonstrates the correct governance problems and business risks. The remaining improvement is not additional domain content, but designated remediation enforcement: authority filtering, temporal policy selection, and duplicate claim quarantining. These changes convert the microcosm from a descriptive domain model into an architecture that is suitable for governance-aware AI decision support.

This log should be used as the formal submission artifact for the Team 2 architecture remediation review.
