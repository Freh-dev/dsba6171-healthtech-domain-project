# Assignment 3 — Section 2: Chunking Experiment Evidence

**Domain:** HealthTech payer claims review  
**Question (unchanged):** What is the annual deductible for the Gold PPO plan in 2025?  
**Documents:** Same five selected HealthTech documents (2 PDF, 2 Markdown, 1 TXT)  
**Embedding:** `nomic-embed-text:latest` (Ollama)  
**Retriever:** n8n Simple Vector Store, Get Many, **K = 4**, metadata included  
**Splitter:** Recursive Character Text Splitter; sizes/overlaps below are **characters**, not tokens.

| Metric | Baseline 1000 / 200 | Alternative 400 / 50 |
| --- | --- | --- |
| In-memory store key | `health_claims_1000` | `health_claims_400` |
| Indexed chunks | **23** | **48** |
| First directly answer-bearing rank | **2** | **1** |
| Similarity of that chunk | **0.7712203475252554** | **0.8069344851350617** |
| Directly relevant chunks at K=4 | **1/4** | **1/4** |
| Wrong-plan Silver HMO in Top-4 | **Yes, rank 4** | **No** |
| Deductible table with both network headings and amounts retained | **Yes, lines 51–69** | **Yes, lines 51–59** |

## Baseline Top-4 — 1000 / 200

| Rank | Source / section | Score | Relevance |
| --- | --- | ---: | --- |
| 1 | Gold PPO 2025, introduction, extracted lines 1–12 | 0.79131552262773 | Partly relevant (plan/year, no deductible figure) |
| 2 | Gold PPO 2025, cost-sharing table, lines 51–69 | 0.7712203475252554 | **Directly relevant** ($1,000 in-network / $2,000 out-of-network individual deductible) |
| 3 | Gold PPO 2025, closing notes, lines 93–97 | 0.7404586489096082 | Irrelevant to amount |
| 4 | Silver HMO 2024, cost-sharing table, lines 45–65 | 0.7214313377057021 | Irrelevant / wrong plan and year |

## Alternative Top-4 — 400 / 50

| Rank | Source / section | Score | Relevance |
| --- | --- | ---: | --- |
| 1 | Gold PPO 2025, cost-sharing table, extracted lines 51–59 | 0.8069344851350617 | **Directly relevant** ($1,000 in-network / $2,000 out-of-network individual deductible) |
| 2 | Gold PPO 2025, introduction, lines 1–10 | 0.7827688450957149 | Partly relevant (plan/year, no deductible figure) |
| 3 | Gold PPO 2025, EOC closing fragment, lines 96–97 | 0.752113091841522 | Irrelevant to amount |
| 4 | Gold PPO 2025, covered services overview, lines 14–22 | 0.723472071724935 | Partly relevant at most (generic cost-sharing context; no deductible figure) |

## Interpretation

The 400/50 configuration generated 25 more chunks (48 versus 23; approximately 108.7% more). With the same five sources, question, embedding model, and K, the answer-bearing deductible table improved from rank 2 to rank 1. The smaller chunk preserves its column headings and deductible amounts while excluding unrelated exclusions from the larger baseline chunk. A wrong-plan Silver HMO result present in the baseline Top-4 was not in the alternative Top-4. Nonetheless, directly relevant precision@4 remained 1/4 for each configuration, and the alternative still retrieved an irrelevant EOC closing fragment. Changing both size and overlap together does not isolate either variable's causal effect. One question does not establish general superiority across claim scenarios.

## Metadata caveat

The indexed chunks contain `source_document`, `source_url`, `plan_type`, `plan_year`, `authority_status`, `jurisdiction`, and extracted-text `loc.lines`. The team's `metadata/document_metadata.csv` contains additional governing fields including `doc_id`, `doc_type`, `effective_date`, `version`, `owner`, `confidentiality` and `access_group`, but these are not all injected into the index. For example, `HLT-STATE-CA` is version 2026 / Current, effective 2026-01-01 in the canonical inventory and source header, whereas the ingestion node stamps 2025 / Approved. Source/file lineage exists, but automatic point-in-time authority and access controls are not implemented.

## Reproducibility and provenance

These values were transcribed from the actual n8n workflow outputs supplied by the tester. The accompanying `section2_baseline_1000_200_k4.json`, `section2_alternative_400_50_k4.json`, workflow exports, and screenshots should be committed separately. Scores are per-query/per-chunk similarity values; do not interpret them alone as retrieval quality metrics.
