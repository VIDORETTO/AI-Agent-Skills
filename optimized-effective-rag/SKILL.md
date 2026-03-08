---
name: optimized-effective-rag
description: |
  Use this skill for ANY task involving RAG (Retrieval-Augmented Generation) pipelines — 
  whether building from scratch, debugging, or optimizing. Trigger on keywords like:
  "RAG", "embeddings", "chunking", "vector search", "semantic search", "retrieval",
  "grounded answers", "document Q&A", "knowledge base", "hallucination in search",
  "recall", "BM25", "rerank", "context packing", "pipeline de documentos",
  "busca semântica", "respostas com fonte", "chunks", "indexação vetorial".
  Also trigger when the user says their RAG is giving wrong answers, not finding
  the right documents, or producing responses without citations. This skill covers
  ALL file formats (PDF, DOCX, XLSX, HTML, Markdown, OCR scans, emails, catalogs)
  and ALL pipeline stages from ingestion to grounded response generation.
  Always use this skill — do not attempt RAG from memory alone.
---

# Optimized & Cost-Effective RAG (v4)

## Quick-Start (5 Steps)

For new pipelines, follow this minimum viable order:

1. **Ingest** → extract `raw_text` + metadata per page/section
2. **Normalize** → Unicode NFKC, whitespace, simple OCR fixes (Stage A)
3. **Chunk** → semantic, 300–500 tokens, title + 2-line overlap
4. **Index** → BM25 + embeddings in parallel
5. **Answer** → top-40 merge → MMR → pack 12 chunks → generate with citation

For deep-dive on any stage, read the sections below or the reference files.

---

## Operational Defaults (Inline — no external dependency)

| Parameter | Default | When to adjust |
|---|---|---|
| Chunk target | 300–500 tokens | ↓ for FAQs/catalogs; ↑ for long narratives |
| Overlap | title + 2 lines | ↑ for numbered-step manuals |
| BM25 top-k | 30 | ↑ if corpus > 500k docs |
| Vector top-k | 30 | ↑ for highly semantic queries |
| Merge top-k | 60 | BM25+vector before MMR |
| Context packing | 10–15 chunks | ↓ for models with < 8k window |
| Token budget | 3,500 tokens | adjust per model and cost |
| MMR lambda | 0.6 | ↑ for relevance; ↓ for diversity |
| Rerank delta threshold | 0.15 | if top1−top3 < 0.15, activate rerank |
| OCR confidence threshold | 0.75 | below → Stage B (LLM fallback) |
| Repeated-line threshold | 60% of pages | → noise, remove from canonical_text |

---

## Pipeline Flow

```
Ingest → Normalize (A+B) → Chunk → Enrich
    ↓
Index (BM25 + Vector)
    ↓
Query → Router → Parallel Retrieval → Merge+MMR → Rerank? → Pack → Grounded Response → Logs
```

---

## Stage 1 — Ingestion & Structuring

Extract `raw_text` preserving structure (headings, sections, tables).
Collect mandatory metadata per unit:

```json
{
  "doc_id": "uuid-or-hash",
  "file": "manual_v3.pdf",
  "page": 12,
  "section_path": "Chapter 3 > Installation > Prerequisites",
  "offset_start": 4820,
  "offset_end": 5340,
  "content_type": "tutorial"
}
```

`content_type` options: `narrative | table | tutorial | catalog | ocr`
→ Determines parser and chunking strategy (see Stage 4).

**For multi-modal content** (images, diagrams): see `references/multimodal.md`

---

## Stage 2 — Two-Stage Normalization

### Stage A — Deterministic (always active)

```python
import unicodedata, re

def normalize_stage_a(text: str, abbrev_map: dict = {}) -> str:
    text = unicodedata.normalize("NFKC", text)
    text = re.sub(r'[\x00-\x08\x0b\x0c\x0e-\x1f\x7f]', '', text)
    text = re.sub(r'[ \t]+', ' ', text)
    text = re.sub(r'\n{3,}', '\n\n', text)
    text = re.sub(r'(?<=\d)l(?=\d)', '1', text)   # OCR: l→1
    text = re.sub(r'(?<=\d)O(?=\d)', '0', text)   # OCR: O→0
    for abbr, expansion in abbrev_map.items():
        text = re.sub(rf'\b{re.escape(abbr)}\b', expansion, text)
    return text.strip()
```

### Stage B — LLM Fallback (objective triggers only)

| Trigger | Criterion |
|---|---|
| Bad OCR | avg confidence < 0.75 |
| Broken table | > 3 inconsistently merged cells |
| Out-of-order sections | H2 appears after H3 without H2 parent |
| Very noisy content | ratio `[^a-zA-Z0-9\s]` > 35% |

Mandatory Stage B prompt:
> "Fix the structure of the text below to be readable and coherent. **Do NOT summarize — preserve all content.** Return only the corrected text."

---

## Stage 3 — Noise Reduction by Signature

```python
from collections import Counter

def remove_repeated_lines(pages: list[str], threshold=0.6) -> list[str]:
    all_lines = [l.strip() for page in pages for l in page.split('\n') if l.strip()]
    counts = Counter(all_lines)
    noise = {l for l, c in counts.items() if c / len(pages) >= threshold}
    return ['\n'.join(l for l in p.split('\n') if l.strip() not in noise) for p in pages]
```

Lines removed from `canonical_text` are kept in `raw_text` for auditing.

---

## Stage 4 — Chunking by Content Type

| Type | Strategy | Target tokens | Overlap |
|---|---|---|---|
| Narrative | Semantic by paragraph | 400–500 | title + 2 lines |
| Tutorial/steps | Semantic + adjacency | 300–400 | previous full step |
| Table | By row/row group | 150–300 | header always |
| Catalog/SKU | Per item + attributes | 100–200 | none |
| OCR | Fixed + wide overlap | 300 | 50 tokens |

**Parent-child** (recommended for manuals):
- `child`: 150–200 tokens → used for recall
- `parent`: 400–600 tokens → used in response packing

**Mandatory chunk metadata:**

```json
{
  "chunk_id": "sha256-short",
  "parent_id": "sha256-parent-or-null",
  "provenance": { "doc_id": "...", "page": 12, "section_path": "..." },
  "tokens": 342,
  "quality": {
    "confidence": 0.91,
    "signals": { "ocr_ok": true, "has_title": true, "is_footer_noise": false },
    "issues": []
  }
}
```

---

## Stage 5 — Aliases & Technical Code Normalization

### Aliases
- LLM-generated aliases **only after human validation or high-confidence regex**.
- Hard prohibitions: brand/model invention, cross-item alias conflicts, out-of-domain acronym expansion.

### Technical Codes

```python
def normalize_codes(text: str) -> str:
    text = re.sub(r'([A-Z]{2,})(\d+)', r'\1 \2', text)     # "CG150" → "CG 150"
    text = re.sub(r'\b(terceira|3ª)\b', '3a', text, flags=re.IGNORECASE)
    text = re.sub(r'N\.?m\b', 'N·m', text)
    text = re.sub(r'\bkw\b', 'kW', text, flags=re.IGNORECASE)
    return text
```

Part numbers: tag with `[[SKU:value]]` before normalization to protect them.

---

## Stage 6 — Hybrid Retrieval & Context Packing

```python
def retrieve_and_pack(query, bm25_index, vector_index,
                      token_budget=3500, mmr_lambda=0.6):
    # 1. Parallel retrieval
    bm25_hits = bm25_index.search(query, top_k=30)
    vec_hits   = vector_index.search(query, top_k=30)

    # 2. Minimal query rewriting (1 lexical + 1 semantic)
    bm25_hits += bm25_index.search(extract_keywords(query), top_k=10)
    vec_hits  += vector_index.search(paraphrase_query(query), top_k=10)

    # 3. Merge + dedupe by chunk_id
    merged = deduplicate(bm25_hits + vec_hits)

    # 4. MMR for diversity
    selected = mmr(merged, lambda_=mmr_lambda, max_results=20)

    # 5. Pack by token budget (NOT fixed count)
    packed, total = [], 0
    for chunk in selected:
        if total + chunk['tokens'] > token_budget:
            break
        packed.append(chunk)
        total += chunk['tokens']

    # 6. Max 3 chunks per section_path (source diversity)
    return enforce_source_diversity(packed, max_per_section=3)
```

**For caching strategies and streaming responses**: see `references/advanced.md`

---

## Stage 7 — Rerank: When to Enable

```python
def should_rerank(query, scores, intent):
    top_delta = scores[0] - scores[2] if len(scores) >= 3 else 1.0
    has_code_or_sku = bool(re.search(r'\b[A-Z]{2,}\s?\d+\b|\bSKU\b', query))
    long_query = len(query.split()) > 8
    is_vague = intent in ('howto', 'comparison', 'explanation')

    if has_code_or_sku and top_delta >= 0.15:
        return False   # high confidence + specific query → skip
    if top_delta < 0.15:
        return True    # scores too close → rerank
    if long_query and is_vague:
        return True    # complex/vague query → rerank
    return False
```

Long queries with lists → decompose into subqueries, retrieve individually, union with dedupe.

---

## Stage 8 — Prompt Engineering & Grounded Response

**Mandatory prompt order:**
1. Output contract (JSON schema)
2. Uncertainty rules ("if no evidence, say X")
3. Short examples (1–2)
4. Validation instruction ("verify your response follows the schema")

**Response template:**

```json
{
  "answer": "...",
  "confidence": "high|medium|low",
  "evidence": [
    {
      "doc_id": "manual_v3.pdf",
      "page": 12,
      "section": "Chapter 3 > Installation",
      "excerpt": "1-2 line excerpt from chunk"
    }
  ],
  "conflict_detected": false,
  "conflict_note": null
}
```

**Fallback messages:**
- No evidence: *"Insufficient evidence found. Try rephrasing or consult [section X]."*
- Conflicting sources: *"Conflict detected: [source A] states X while [source B] states Y."*
- Invalid JSON repair prompt: *"Fix only the JSON below to be valid. Do not change any content values."*

---

## Security & Privacy

- **PII**: redact CPF, email, phone, full names in `canonical_text` for sensitive corpora.
- **ACL**: apply `doc_id`/client filter **before** retrieval, never after.
- **Logging**: log only hashes and IDs — never sensitive content.

---

## Versioning & Monitoring

```json
{
  "normalization_version": "2.1.0",
  "embedding_model": "text-embedding-3-large",
  "embedding_model_version": "2024-03",
  "indexed_at": "2025-06-01T14:22:00Z"
}
```

- **Reindex** when changing normalizer or embedding model.
- **Monitor drift** weekly: if `Recall@10` drops > 5pp → investigate.

---

## Quick Diagnostics

| Symptom | Likely cause | Action |
|---|---|---|
| Correct answers but no source | Grounding off or `evidence` empty | Check prompt template and schema |
| Irrelevant chunks in top-5 | BM25 dominating semantic queries | Increase vector weight in merge |
| Hallucination in tables | Chunking breaking tables | Use "per-row + header" strategy |
| Low recall on codes | Code normalization missing | Enable `normalize_codes()` before indexing |
| Latency > 3s | Rerank always on | Apply Stage 7 trigger heuristic |
| Generic answers without detail | Token budget too low | Increase from 3,500 → 5,000 tokens |
| Duplicate chunks in context | Dedupe not active | Add exact hash + near-duplicate check |

---

## Anti-Patterns

| Anti-pattern | Why it's bad | Fix |
|---|---|---|
| LLM for all normalization | High cost, latency, risk of unwanted summarization | Use deterministic Stage A; B only with trigger |
| Fixed chunk count packing | Variable chunk sizes waste or inflate context | Pack by token budget |
| Rerank always on | Unnecessary latency for specific SKU/code queries | Use trigger heuristic (delta + intent) |
| Aliases without validation | Can create conflicts or invent brands/models | Human validation or high-confidence regex |
| Mixing conflicting sources silently | Inconsistent answers, user loses trust | Detect conflict and surface both sources |
| No versioning | Can't reproduce or rollback | Persist `normalization_version` + `embedding_model_version` |
| ACL after retrieval | Sensitive data leakage even if not displayed | ACL before retrieval |

---

## Quality Checklist

- [ ] Operational defaults applied
- [ ] Stage A normalization active on all ingestion
- [ ] Stage B only triggered by objective criteria
- [ ] Dedupe: exact hash + near-duplicate by embedding
- [ ] MMR active in merge with configured lambda
- [ ] Packing by token budget (not count)
- [ ] Rerank heuristic with explicit trigger
- [ ] JSON schema + repair step + validator in prompt
- [ ] Responses with citation: doc_id, page, excerpt
- [ ] ACL/PII applied before retrieval
- [ ] Normalizer and embedding versioning persisted
- [ ] Evaluation with metrics before any promotion

---

## Evaluation Gates (Required Before Any Promotion)

| Metric | Formula | Minimum threshold |
|---|---|---|
| Recall@10 | relevant chunks in top-10 / total relevant | ≥ 0.80 |
| MRR | mean(1/rank of first relevant) | ≥ 0.65 |
| Grounding rate | responses with valid citation / total | ≥ 0.90 |
| Hallucination rate | responses with ungrounded fact / total | ≤ 0.05 |
| p95 latency | ms for full pipeline | ≤ 3,000ms |

**Promotion gate**: all metrics above threshold on holdout set of ≥ 50 queries.
**Canary**: deploy to 5% of traffic, monitor 24h, rollback if any metric drops.

---

## Reference Files

For advanced topics, read these files when relevant:

- `references/multimodal.md` — Images, diagrams, charts, audio transcripts in RAG
- `references/advanced.md` — Caching, streaming, cost optimization, GraphRAG, long-doc strategies
- `references/eval-harness.md` — Full evaluation harness with test set templates and scoring scripts