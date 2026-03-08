# Multi-modal RAG Reference

## When to Read This File
Read when the corpus contains images, diagrams, charts, scanned pages, audio transcripts,
or any non-text content that needs to be searchable.

---

## Image & Diagram Handling

### Strategy 1 — Caption Extraction (fast, cheap)
Extract alt text, captions, and surrounding context. Index these as a text chunk
with `content_type: "image_caption"`. Works well for figures with descriptive captions.

```json
{
  "chunk_id": "img-sha256",
  "content_type": "image_caption",
  "text": "Figure 4.2: Torque curve for CG 150 engine at 6000 RPM",
  "provenance": { "doc_id": "manual_v3.pdf", "page": 34 },
  "image_ref": "s3://bucket/manual_v3/p34_fig4.png"
}
```

### Strategy 2 — Vision LLM Description (accurate, moderate cost)
Pass each image through a vision model to generate a structured description.
Use for technical diagrams, schematics, wiring maps, charts.

Prompt template:
> "Describe this image concisely for search indexing. Include: type of image,
> key labels, numbers, and relationships visible. Do not invent details not shown.
> Max 150 words."

Store the description as `text` in the chunk, keep `image_ref` for rendering in response.

### Strategy 3 — Multimodal Embeddings (best recall, higher cost)
Use models like `clip-vit-large` or `nomic-embed-vision` to embed images directly.
Maintain a separate vector index for image embeddings.
Query routing: if query contains visual keywords ("diagram", "schematic", "figure"),
include image index in parallel retrieval.

---

## Scanned Pages (OCR)

### Confidence-Gated Pipeline

```python
def process_scanned_page(image_path: str, ocr_engine) -> dict:
    result = ocr_engine.extract(image_path)
    avg_confidence = sum(w.confidence for w in result.words) / len(result.words)
    
    if avg_confidence >= 0.75:
        # Stage A normalization is enough
        return {"text": normalize_stage_a(result.text), "ocr_confidence": avg_confidence}
    else:
        # Stage B: pass image + raw OCR to vision LLM for cleanup
        return llm_ocr_repair(image_path, result.text)
```

### LLM OCR Repair Prompt
> "The following text was extracted by OCR from a scanned document but contains errors.
> Correct the text using the image for reference. Preserve all content — do not summarize.
> Return only the corrected text."

---

## Charts & Tables as Images

For charts embedded as images (not native PDF tables):
1. Use vision LLM to extract data as structured text or CSV
2. Store extracted data as a `table` content_type chunk
3. Add caption describing what the chart shows

```json
{
  "content_type": "chart_extracted",
  "text": "Sales data 2023 Q1-Q4: Q1=1200, Q2=1450, Q3=1380, Q4=1900 units",
  "source_note": "Extracted from bar chart via vision model",
  "image_ref": "..."
}
```

---

## Audio Transcripts

1. Transcribe with Whisper or equivalent → raw transcript
2. Apply Stage A normalization + speaker diarization cleanup
3. Chunk by semantic breaks (topic shifts), not fixed windows
4. Index as `content_type: "transcript"`
5. Include timestamp metadata for citation:

```json
{
  "content_type": "transcript",
  "text": "...",
  "provenance": { "doc_id": "meeting_2024_03.mp3", "timestamp_start": "00:12:34", "timestamp_end": "00:14:02" }
}
```

---

## Citation in Multi-modal Responses

When the answer is grounded in an image or chart, include `image_ref` in evidence:

```json
{
  "evidence": [
    {
      "doc_id": "manual_v3.pdf",
      "page": 34,
      "section": "Chapter 4 > Performance Charts",
      "excerpt": "Torque curve showing peak at 6000 RPM",
      "image_ref": "s3://bucket/manual_v3/p34_fig4.png",
      "source_type": "image"
    }
  ]
}
```

---

## Cost Optimization for Multi-modal

| Content | Cheap option | Accurate option |
|---|---|---|
| Images with captions | Caption extraction only | Vision LLM description |
| Scanned pages | OCR + Stage A | OCR + LLM repair (Stage B) |
| Charts | Skip or caption | Vision extraction to structured text |
| Audio | Fast Whisper model | Large Whisper + speaker diarization |

**Rule of thumb**: Use cheap option first. Switch to accurate option only if
Recall@10 on multi-modal queries is below threshold.