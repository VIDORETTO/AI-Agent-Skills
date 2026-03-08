---
name: landing-site-builder
description: >
  Use SEMPRE que o usuário pedir para criar, estruturar ou planejar uma landing page ou site institucional —
  mesmo que ele use palavras como "página", "site", "LP", "one-pager", "site de apresentação", "página de vendas",
  "captura de leads", "site da empresa", "portfólio de serviços" ou "site de agendamento".
  Gera um pacote completo e publicável: brief consolidado, estratégia de mensagem, arquitetura,
  copy por seção, mini design system e QA — com opção de entregar código (HTML/CSS ou React/Next).
  Acione esta skill assim que o pedido envolver QUALQUER combinação de: estrutura de página, copy de site,
  design para web, CTA, conversão online ou presença digital para um produto/serviço/empresa.
  NÃO use apenas para: ajustes pontuais em texto já existente, logos isolados, posts de redes sociais,
  e-mails avulsos ou sistemas com backend/login.
metadata:
  version: "2.0.0"
  owner: "user"
---

# Landing Page & Site Institucional Builder

## O que esta skill entrega

A partir de um brief (completo ou parcial), gera um **pacote estruturado e verificável**:

| Arquivo | Conteúdo |
|---|---|
| `01_brief.md` | Brief consolidado + lacunas + assunções rotuladas |
| `02_mensagem.md` | Promessa, 3 headlines, 5 diferenciais, 5 objeções + respostas |
| `03_arquitetura.md` | LP: seções ordenadas; Institucional: sitemap + intenção por página |
| `04_copy.md` | Copy completo por seção + microcopy de formulários |
| `05_design-system.md` | Direções A/B + tokens + componentes |
| `06_qa.md` | Rubrica 0–5 por critério + top 10 correções + checklist de publicação |

**Opcional:** pasta `code/` com implementação em HTML+CSS ou React/Next.

---

## Passo 0 — Classificar o pedido antes de tudo

Antes de qualquer output, identificar:

1. **Tipo**: `LP` (1 página, 1 conversão) ou `Institucional` (sitemap + N páginas)?
   - Dúvida → perguntar. Regra prática: se há menu de navegação e múltiplos objetivos → Institucional.
2. **Brief disponível?**
   - Sim (completo) → avançar direto para Passo 2.
   - Sim (parcial) → listar lacunas, registrar assunções, avançar.
   - Não → gerar as perguntas do brief (ver `assets/brief-template.md`) e **aguardar** resposta antes de escrever copy.

> **Regra de ouro:** nunca inventar dados (números, clientes, preços, depoimentos). Lacunas viram `ASSUNÇÃO:` ou perguntas explícitas.

---

## Passo 1 — Consolidar o brief (`01_brief.md`)

Normalizar as informações fornecidas. Para cada campo em branco:
- Se for possível deduzir com alta confiança → registrar como `ASSUNÇÃO: [raciocínio]`
- Se for crítico para o copy (preço, CTA, público) → perguntar explicitamente

Campos essenciais (ver template completo em `assets/brief-template.md`):
- Tipo, objetivo primário, CTA primário, oferta, público, provas reais, tom, stack (opcional), restrições legais.

---

## Passo 2 — Estratégia de mensagem (`02_mensagem.md`)

Produzir:
- **Promessa principal** (1 frase, específica, orientada a benefício)
- **3 opções de headline** + 1 subheadline para cada
- **5 diferenciais** (concretos, não genéricos como "qualidade")
- **5 objeções** prováveis + resposta curta para cada

Critério de qualidade: a promessa deve responder "o que eu ganho?" em menos de 8 segundos de leitura.

---

## Passo 3 — Arquitetura (`03_arquitetura.md`)

**Para LP:**
Listar seções em ordem de conversão. Para cada seção: objetivo, mensagem-chave, elementos visuais sugeridos, CTA (primário ou secundário).

Ordem sugerida (adaptar conforme oferta):
`Hero → Problema/Contexto → Solução → Como funciona → Prova social → Preços/Planos (se houver) → FAQ → CTA final → Footer`

**Para Institucional:**
Sitemap com intenção por página. Mínimo: Home, Sobre, Serviços/Produto, Contato, Privacidade.
Para cada página: objetivo, conteúdo-chave, CTA ou ação esperada.

---

## Passo 4 — Copy por seção (`04_copy.md`)

Para cada seção/página da arquitetura, escrever:
- H1/H2/H3 definitivos
- Parágrafos de suporte (máx. 3 linhas cada)
- Bullets de benefícios ou features (quando aplicável)
- CTA principal + variação alternativa
- **Microcopy de formulários**: placeholder, label, mensagem de ajuda, erro, confirmação

Tom: seguir o definido no brief. Se não definido → direto, sem jargão, conversacional.

---

## Passo 5 — Mini design system (`05_design-system.md`)

Propor **Direção A** e **Direção B** com:
- Conceito (2 frases, ex.: "minimalista + autoridade" vs "vibrante + acessível")
- Paleta: primary, secondary, background, text, accent, error
- Tipografia: fonte(s), escala (xs → 3xl), peso(s)
- Espaçamentos e grid (container, colunas, gaps)
- Border-radius e sombras
- Componentes-chave: Header, Hero, Botão (primário/secundário/ghost), Card, Depoimento, FAQ, Form, Footer

Se o cliente já tiver brand guide → adaptar e apontar divergências, não substituir.

---

## Passo 6 — Código (opcional)

Gerar somente se o usuário pediu explicitamente ou confirmou quando perguntado.

Stack padrão se não especificada: `HTML + CSS` (sem build). Para múltiplas páginas ou componentes reutilizáveis → sugerir React/Next.

Regras obrigatórias de implementação:
- Semântica HTML (`header/main/section/footer`, botões para ações, links para navegação)
- Mobile-first (breakpoint base ≤ 375px)
- A11y mínima: headings em ordem, labels em inputs, foco visível, alt em imagens informativas
- SEO básico: `<title>`, `<meta name="description">`, H1 único, Open Graph
- Performance: imagens com `width`/`height` definidos, sem fontes desnecessárias

Para detalhes avançados → ler `references/implementation-notes.md`.

---

## Passo 7 — QA final (`06_qa.md`)

Avaliar o pacote completo com a rubrica abaixo. Para cada critério, dar nota 0–5 + problema identificado + correção sugerida.

| Critério | O que avaliar |
|---|---|
| Clareza da proposta | O visitante entende o que é, pra quem é e o benefício em < 8s? |
| CTA e conversão | CTAs claros, recorrentes, microcopy sem fricção? |
| Credibilidade | Há prova real? Nenhum dado inventado? |
| Usabilidade | Hierarquia visual, escaneabilidade, consistência? |
| Mobile-first | Primeiro scroll funciona em 375px? |
| Acessibilidade | Headings, foco, labels, contraste? |
| Performance | Sem imagens pesadas, sem CLS óbvio? |
| SEO básico | Title/description, H1 único, headings semânticos? |

Finalizar com: **top 10 correções por impacto** + **checklist de publicação**.

Para a rubrica completa e checklist de publicação → ler `references/qa-rubric.md`.

---

## Edge cases rápidos

| Situação | Ação |
|---|---|
| Brief vago ("quero um site bonito") | Gerar `01_brief.md` com perguntas. Não avançar para copy sem hipóteses confirmadas. |
| Múltiplos CTAs competindo | Eleger 1 CTA primário por página; demais viram secundários com hierarquia visual menor. |
| Sem prova social | Sugerir substitutos reais: processo transparente, garantias, bastidores, credenciais. Nunca inventar. |
| Oferta complexa | Criar seção "Como funciona" com 3–5 passos. Simplificar acima da dobra. |
| Stack indefinida | Entregar deliverables; perguntar se quer `HTML+CSS` ou `React/Next` antes de gerar código. |
| Já tem brand guide | Extrair tokens do guia; apontar conflitos com sugestões do design system. |
| Restrições legais (LGPD) | Incluir página/link de Privacidade e banner de consentimento quando houver coleta de dados. Não dar aconselhamento jurídico. |

---

## Arquivos de referência

Leia quando necessário (não carregue todos de uma vez):

- `assets/brief-template.md` — Template de brief completo para enviar ao cliente
- `references/qa-rubric.md` — Rubrica detalhada 0–5 + checklist de publicação
- `references/implementation-notes.md` — Guia de código (semântica, a11y, performance, SEO)
- `references/seo-a11y-performance.md` — Checklists rápidos de SEO, acessibilidade e performance
