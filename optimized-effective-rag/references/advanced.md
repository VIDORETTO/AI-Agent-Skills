# Advanced RAG Reference

## When to Read This File
Read when you need: semantic caching, streaming responses, cost reduction strategies,
GraphRAG for relationship-heavy corpora, long-document handling, or multi-tenant setups.

---

## Semantic Caching

Cache not just exact queries but semantically similar ones.

```python
class SemanticCache:
    def __init__(self, vector_store, similarity_threshold=0.92):
        self.store = vector_store
        self.threshold = similarity_threshold

    def get(self, query: str) -> dict | None:
        q_embedding = embed(query)
        hits = self.store.search(q_embedding, top_k=1)
        if hits and hits[0].score >= self.threshold:
            return hits[0].cached_response  # Cache hit
        return None

    def set(self, query: str, response: dict):
        self.store.upsert({
            "embedding": embed(query),
            "query": query,
            "cached_response": response,
            "cached_at": datetime.utcnow().isoformat()
        })
```

**Cache invalidation**: expire entries when the underlying chunks are reindexed.
Tie cache TTL to `indexed_at` timestamp — if a chunk's `indexed_at` is newer than
the cache entry, invalidate.

**Cache hit rate target**: > 30% for production FAQ-heavy corpora.

---

## Streaming Responses

Stream the generation while still providing citations at the end.

```python
async def stream_grounded_response(packed_chunks, query, llm_client):
    # Build context string
    context = format_chunks_for_prompt(packed_chunks)
    
    # Stream the answer portion
    answer_tokens = []
    async for token in llm_client.stream(
        system="Answer based only on context. End with [CITATIONS_FOLLOW].",
        user=f"Context:\n{context}\n\nQuestion: {query}"
    ):
        if "[CITATIONS_FOLLOW]" not in token:
            answer_tokens.append(token)
            yield {"type": "token", "content": token}
    
    # Emit citations as a single structured event
    citations = extract_citations(packed_chunks)
    yield {"type": "citations", "content": citations}
```

**UI pattern**: render answer tokens live, append citation panel after stream completes.

---

## Cost Optimization

### Tiered Embedding Strategy

Not all chunks need expensive embeddings. Use a tiered approach:

| Tier | Content | Model | Cost |
|---|---|---|---|
| Tier 1 | High-value (titles, summaries) | `text-embedding-3-large` | High |
| Tier 2 | Standard content | `text-embedding-3-small` | Low |
| Tier 3 | Noise-risk content (OCR < 0.85) | BM25 only, no embedding | Zero |

### Context Window Cost Control

```python
def pack_by_budget(chunks, token_budget=3500):
    """Always pack by token budget, never by fixed count."""
    packed, total = [], 0
    for chunk in chunks:
        cost = chunk['tokens']
        if total + cost > token_budget:
            break
        packed.append(chunk)
        total += cost
    return packed
```

**Savings tip**: for simple factual queries (detected by intent classifier as `lookup`),
reduce token_budget to 1,500 and skip rerank. Reserve full budget for `explanation`
and `comparison` intents.

### Batch Reindexing

Never reindex one chunk at a time. Batch in groups of 1,000:

```python
def batch_embed(chunks, batch_size=1000):
    for i in range(0, len(chunks), batch_size):
        batch = chunks[i:i+batch_size]
        embeddings = embedding_model.embed([c['text'] for c in batch])
        yield zip(batch, embeddings)
```

---

## GraphRAG (Relationship-Heavy Corpora)

Use when corpus has strong entity relationships: org charts, parts catalogs with
dependencies, legal documents with cross-references, codebases.

### When to Use GraphRAG vs Standard RAG

| Scenario | Use |
|---|---|
| "What depends on component X?" | GraphRAG (relationship traversal) |
| "Summarize section 3.2" | Standard RAG |
| "Which parts are compatible with model Y?" | GraphRAG |
| "What does the manual say about torque?" | Standard RAG |

### Lightweight GraphRAG Implementation

```python
# During indexing: extract entity relationships
def extract_relationships(chunk_text, llm_client) -> list[dict]:
    # Prompt for structured relationship extraction
    return llm_client.extract(
        prompt=f"Extract entity relationships from: {chunk_text}",
        schema={"subject": str, "predicate": str, "object": str, "chunk_id": str}
    )

# During retrieval: expand query via graph traversal
def graph_expand(entity: str, graph_db, hops=2) -> list[str]:
    """Returns chunk_ids reachable within N hops from entity."""
    return graph_db.traverse(start=entity, max_hops=hops, direction="both")
```

**Merge graph results** with standard vector/BM25 results before MMR. Give graph
results a 1.2x score boost since they're relationship-validated.

---

## Long Document Strategies

For documents > 100 pages or > 150k tokens:

### Hierarchical Summarization Index

```
Full Doc
├── Doc Summary (1 chunk, ~500 tokens)  ← indexed separately
├── Chapter Summaries (1 per chapter)   ← indexed separately
└── Standard Chunks (300–500 tokens)    ← main index
```

**Query routing**:
- Vague/overview queries → search summary index first, then expand
- Specific queries → search standard chunk index directly

### Sliding Window for Dense Narratives

For legal contracts, academic papers — use sliding window with 50% overlap
to ensure no clause boundary is missed:

```python
def sliding_chunks(text, chunk_size=400, overlap=200):
    tokens = tokenize(text)
    for start in range(0, len(tokens) - chunk_size, chunk_size - overlap):
        yield tokens[start:start + chunk_size]
```

---

## Multi-Tenant RAG

When serving multiple clients from a single index:

### Hard Isolation (recommended for sensitive data)
- Separate vector index per tenant
- Higher cost, zero cross-contamination risk

### Soft Isolation (cost-efficient)
- Shared index with `tenant_id` metadata filter
- Apply filter **before** retrieval (never after)

```python
def retrieve_for_tenant(query, tenant_id, vector_index):
    # ALWAYS pre-filter — never retrieve then filter
    return vector_index.search(
        query,
        top_k=30,
        filter={"tenant_id": {"$eq": tenant_id}}
    )
```

**Audit log** every retrieval with `tenant_id` + `query_hash` (not raw query).

---

## Performance Tuning Cheatsheet

| Problem | Lever | Direction |
|---|---|---|
| High latency on BM25 | BM25 top-k | ↓ from 30 to 15 for simple queries |
| High latency on embedding | Batch size | ↑ for throughput; async calls |
| High LLM cost | Token budget | ↓ or add query-intent classifier |
| Low diversity in results | MMR lambda | ↓ (more diversity) |
| Missing niche terms | BM25 weight | ↑ in hybrid merge |
| Missing semantic matches | Vector weight | ↑ in hybrid merge |
| Slow rerank | Cross-encoder model | Switch to lighter bi-encoder for stage 1 |