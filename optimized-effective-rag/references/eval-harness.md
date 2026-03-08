# RAG Evaluation Harness

## When to Read This File
Read when setting up a test suite, grading pipeline quality, or deciding whether
a pipeline change is safe to promote to production.

---

## Evaluation Metrics (Required Before Any Promotion)

| Metric | Formula | Minimum threshold |
|---|---|---|
| Recall@10 | relevant chunks in top-10 / total relevant | ≥ 0.80 |
| MRR | mean(1/rank of first relevant chunk) | ≥ 0.65 |
| Grounding rate | responses with valid citation / total | ≥ 0.90 |
| Hallucination rate | responses with ungrounded fact / total | ≤ 0.05 |
| p95 latency | ms for full pipeline | ≤ 3,000ms |

**Promotion gate**: ALL metrics above threshold on holdout set of ≥ 50 queries.
**Canary**: 5% of traffic, monitor 24h, rollback if any metric drops.

---

## Test Set Template

Minimum 50 queries before promotion. Cover all query types:

```json
[
  {
    "query_id": "q001",
    "query": "What is the maximum torque of the CG 150 engine?",
    "intent": "lookup",
    "expected_doc_ids": ["manual_cg150.pdf"],
    "expected_sections": ["Chapter 5 > Specifications"],
    "answer_contains": ["torque", "N·m"],
    "should_not_contain": ["invented", "estimated"]
  },
  {
    "query_id": "q002",
    "query": "Compare the CG 150 and CG 160 maintenance schedules",
    "intent": "comparison",
    "expected_doc_ids": ["manual_cg150.pdf", "manual_cg160.pdf"],
    "answer_contains": ["CG 150", "CG 160", "km"],
    "should_not_contain": []
  },
  {
    "query_id": "q003",
    "query": "How do I replace the air filter on a CG 150?",
    "intent": "tutorial",
    "expected_doc_ids": ["manual_cg150.pdf"],
    "expected_sections": ["Chapter 7 > Maintenance"],
    "answer_contains": ["step", "filter"],
    "should_not_contain": []
  }
]
```

### Intent Distribution (recommended for 50-query set)

| Intent | Count | Purpose |
|---|---|---|
| `lookup` | 15 | Specific fact retrieval |
| `tutorial` | 10 | Step-by-step procedures |
| `comparison` | 8 | Multi-document synthesis |
| `explanation` | 8 | Concept understanding |
| `code/sku` | 6 | Technical code lookup |
| `negative` | 3 | Queries with no answer in corpus |

---

## Automated Grading Script

```python
def grade_response(response: dict, expected: dict) -> dict:
    """
    response: the RAG pipeline output
    expected: one entry from the test set
    """
    results = {}

    # 1. Grounding: does evidence contain expected doc_ids?
    cited_docs = {e['doc_id'] for e in response.get('evidence', [])}
    results['grounding'] = bool(cited_docs & set(expected['expected_doc_ids']))

    # 2. Answer quality: does answer contain expected keywords?
    answer_lower = response.get('answer', '').lower()
    results['answer_coverage'] = all(
        kw.lower() in answer_lower for kw in expected['answer_contains']
    )

    # 3. Hallucination check: does answer contain forbidden phrases?
    results['no_hallucination'] = not any(
        phrase.lower() in answer_lower for phrase in expected['should_not_contain']
    )

    # 4. Confidence calibration
    results['confidence'] = response.get('confidence', 'low')

    # Overall pass
    results['pass'] = (
        results['grounding'] and
        results['answer_coverage'] and
        results['no_hallucination']
    )

    return results


def run_eval_suite(test_set: list, pipeline_fn) -> dict:
    grades = [grade_response(pipeline_fn(q['query']), q) for q in test_set]
    return {
        "total": len(grades),
        "pass_rate": sum(g['pass'] for g in grades) / len(grades),
        "grounding_rate": sum(g['grounding'] for g in grades) / len(grades),
        "hallucination_rate": sum(not g['no_hallucination'] for g in grades) / len(grades),
        "details": grades
    }
```

---

## Chunk-Level Recall Evaluation

```python
def compute_recall_at_k(retrieved_chunk_ids: list, relevant_chunk_ids: set, k=10) -> float:
    top_k = set(retrieved_chunk_ids[:k])
    hits = top_k & relevant_chunk_ids
    return len(hits) / len(relevant_chunk_ids) if relevant_chunk_ids else 0.0


def compute_mrr(retrieved_chunk_ids: list, relevant_chunk_ids: set) -> float:
    for rank, chunk_id in enumerate(retrieved_chunk_ids, start=1):
        if chunk_id in relevant_chunk_ids:
            return 1.0 / rank
    return 0.0
```

---

## Regression Testing

Run this suite on every pipeline change, including:
- Normalizer version bump
- Embedding model change
- Chunking strategy change
- Rerank model swap
- Prompt template modification

**Compare against baseline** (last promoted version):
- If Recall@10 drops > 3pp → block promotion, investigate
- If hallucination rate increases > 1pp → block promotion, investigate
- If latency p95 increases > 20% → profile and optimize before promoting

---

## Manual Review Sampling

For each evaluation run, manually inspect a random sample:
- 5 `pass` cases → verify quality is genuinely good, not just passing metrics
- 5 `fail` cases → categorize failure type:
  - `missing_chunk`: correct answer not retrieved
  - `wrong_chunk`: wrong chunk ranked first
  - `bad_grounding`: answer ungrounded despite correct retrieval
  - `hallucination`: invented fact not in any chunk
  - `incomplete`: partial answer, missing key detail

Track failure categories over time to identify systematic pipeline weaknesses.

---

## Golden Set Management

Maintain a "golden set" of 20–30 queries that must always pass:
- These are your highest-impact, most common real user queries
- Failing any golden set query = automatic rollback trigger
- Update golden set quarterly as usage patterns evolve

```python
GOLDEN_QUERY_IDS = {"q001", "q005", "q012", "q023", ...}  # your top queries

def check_golden_set(eval_results: list) -> bool:
    golden_results = [r for r in eval_results if r['query_id'] in GOLDEN_QUERY_IDS]
    golden_pass_rate = sum(r['pass'] for r in golden_results) / len(golden_results)
    return golden_pass_rate >= 1.0  # All golden queries must pass
```