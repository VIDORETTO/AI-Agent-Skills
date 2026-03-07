---
name: optimized-effective-rag
description: Use esta skill quando precisar projetar, revisar ou implementar pipelines RAG para qualquer tipo de arquivo, com defaults operacionais claros, normalizacao robusta, retrieval/rerank eficientes e controle de qualidade por metricas.
---

# RAG Eficaz, Otimizado e Economico (v2)

## Quando Usar
Use esta skill para criar ou melhorar aplicacoes RAG com foco simultaneo em:
- eficacia de resposta grounded,
- otimizacao de recuperacao e contexto,
- economia de latencia e custo por query.

Ela cobre PDFs, scans OCR, DOCX, planilhas, HTML, markdown, emails, catalogos tecnicos e formatos mistos.

## Resultado Esperado
Ao aplicar esta skill, voce deve terminar com:
- pipeline executavel de ponta a ponta,
- padrao de normalizacao em 2 estagios,
- retrieval hibrido com diversidade e dedupe,
- rerank condicional com gatilhos objetivos,
- resposta com citacao rastreavel,
- versionamento e observabilidade para evitar degradacao.

## Mapa Mental do Pipeline
- Ingestao -> Normalizacao -> Chunking -> Enriquecimento -> Indexacao
- Query -> Router -> Retrieval -> Re-rank -> Context packing -> Answer grounded -> Logs

## Config Padrao (Starting Defaults)
- Chunk alvo: `250-600 tokens`.
- Overlap: `titulo + 1-2 linhas`.
- Retrieval: `BM25 + embedding`.
- Top-k: lexical `20-60`, vetorial `20-60`, merge `40-120`.
- Context packing: `8-20 chunks` com dedupe/diversidade.
- Re-rank: apenas quando query ambigua/vaga ou scores proximos.

Para valores operacionais completos (X, Y, delta, budget, MMR etc.), use:
- [operational-defaults.md](./references/operational-defaults.md)

## Workflow de Execucao

### 1) Ingestao e Estruturacao
- Coletar `raw_text` e metadados por documento/pagina/secao.
- Preservar proveniencia (`doc_id`, arquivo, pagina, section_path, offsets quando houver).
- Classificar tipo de conteudo (narrativo, tabela, tutorial, catalogo, OCR).

### 2) Normalizacao em 2 Estagios

#### Estagio A (Deterministico, padrao)
- Unicode `NFKC`.
- Remocao de invisiveis.
- Normalizacao de whitespace.
- Correcao simples de OCR por regra.
- Expansao via `abbrev_map`.
- Normalizacao de unidades/numeros/datas.

#### Estagio B (Fallback LLM, sob gatilho)
Disparar somente para:
- tabelas quebradas,
- OCR ruim,
- secoes desordenadas,
- conteudo muito sujo.

Gatilhos e thresholds concretos:
- [operational-defaults.md](./references/operational-defaults.md)

### 3) Reducao de Ruido por Assinatura
- Detectar linhas repetidas em muitas paginas (ex.: >60%).
- Remover de `canonical_text`, manter em `raw_text`.

### 4) Normalizacao Critica para Codigos
- `CG150 -> CG 150`.
- `3ª`, `3a`, `terceira` -> `3a`.
- Padronizar `kW`, `Nm`, `N·m`.
- Marcar part numbers como intocaveis.

### 5) Aliases e Sinonimos com Validacao
- Alias de LLM so entra apos validacao.
- Proibir conflito entre itens.
- Proibir invencao de marca/modelo.

### 6) Chunking Robusto
- Chunking semantico com metadata (`section_path`, pagina, entidades).
- Parent-child (opcional): child para recall, parent para resposta grounded.
- Chunk adjacency: para manuais/passos, permitir vizinhos no packing.
- Dedupe: hash exato + near-duplicate por embedding.

### 7) Retrieval e Context Packing
- Rodar BM25 e vetorial em paralelo.
- Fazer merge e aplicar diversidade (MMR/heuristica).
- Limitar repeticao por `section_path` e parent.
- Pack por budget de tokens (nao por contagem fixa).
- Query rewriting minimo: `1 lexical + 1 semantico`.

### 8) Re-rank Economico
- Skip rerank quando confianca ja esta separada por delta.
- Evitar rerank para query com codigo/SKU (exceto empate).
- Ativar rerank para query vaga/comparativa/how-to.
- Para queries longas com lista: decompor em subqueries e unir com dedupe.

### 9) Prompt Engineering Anti-Inconsistencia
Ordem recomendada:
1. Contrato de saida (JSON)
2. Regras de incerteza
3. Exemplos curtos
4. Validacao

Padroes obrigatorios:
- JSON estrito + schema.
- Repair step: `Corrija apenas o JSON para ficar valido. Nao mude valores.`
- Enums controladas (`intent`, `chunk_type`, etc.).
- Regra de normalizador: `Proibido resumir; preserve conteudo.`

### 10) Resposta Grounded
- Sempre citar `doc_id`, secao, pagina.
- Incluir `1-2 frases` curtas de evidencia.
- Se sem base: usar template de evidencia insuficiente.
- Se fontes conflitarem: expor conflito e citar ambas, sem decidir sem evidencias.

### 11) Seguranca e Privacidade
- Redacao de PII no `canonical_text` quando aplicavel.
- ACL por documento/cliente antes de retrieval.
- Logging com hashes/IDs, sem vazamento de conteudo sensivel.

### 12) Operacao e Versionamento
- Persistir `normalization_version` e `embedding_model_version`.
- Reindexar em mudanca estrutural de normalizacao/embedding.
- Monitorar drift continuamente.

## Decision Tree por Tipo de Arquivo
Para escolher parser/normalizacao/chunking corretos por formato:
- [file-routing.md](./references/file-routing.md)

## Protocolo de Avaliacao (Obrigatorio)
Nao considerar melhoria sem medicao. Use:
- [evaluation-protocol.md](./references/evaluation-protocol.md)

Inclui:
- metrica de retrieval (`Recall@k`, `MRR`, `nDCG`),
- metrica de resposta grounded,
- custo/latencia,
- gate de promocao,
- canary e rollback.

## Blocos Prontos (Copy/Paste)

### Quality score por chunk
```json
"quality": {
  "confidence": 0.0,
  "signals": {
    "ocr_ok": true,
    "has_title": true,
    "has_entities": false,
    "is_footer_noise": false
  },
  "issues": []
}
```

### Metadados de auditoria
```json
"provenance": {
  "file": "...",
  "doc_id": "...",
  "page": 0,
  "section_path": "...",
  "offset_start": null,
  "offset_end": null
},
"hash": {
  "raw": "sha256(...)",
  "canonical": "sha256(...)"
}
```

### Heuristica simples de rerank
- rerank = true se:
  - query tem > 8 palavras e nao tem codigo,
  - ou top1-top3 esta muito proximo,
  - ou intencao = `howto/comparison`.

## O Que Enxugar
- Papeis de agentes: opcional.
- Pode ser um unico servico com etapas.
- Preferir poucos exemplos curtos e de alto valor.

## Checklist de Qualidade
- [ ] Defaults operacionais aplicados.
- [ ] Normalizacao 2 estagios ativa.
- [ ] Dedupe exato + near-duplicate ativo.
- [ ] MMR/diversidade no merge.
- [ ] Packing por budget.
- [ ] Rerank com gatilho explicito.
- [ ] JSON schema + repair + validator.
- [ ] Grounding com citacoes curtas.
- [ ] ACL/PII/logging seguro.
- [ ] Versionamento e monitoramento de drift.

## Anti-Patterns
- LLM para normalizar tudo sem gatilho.
- Packing por quantidade fixa de chunk.
- Rerank sempre ligado.
- Alias sem validacao.
- Misturar fontes conflitantes sem explicitar.
- Operar sem versionamento e sem avaliacao.
