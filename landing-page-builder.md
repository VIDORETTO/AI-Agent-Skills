---
name: landing-site-builder
description: >
  Use quando o usuário pedir para criar uma landing page (1 página, 1 objetivo) ou um site institucional (sitemap + páginas) a partir de um brief,
  gerando um pacote publicável com arquitetura, copy por seção, mini design system e QA; opcionalmente gerar código (HTML/CSS ou React/Next).
  NÃO use quando o pedido for só “um logo”, “um post”, “um e-mail”, “um app”, ou quando já existir design/copy finais e o usuário quer apenas
  pequenos ajustes pontuais (use uma skill de revisão/edição).
metadata:
  version: "1.0.0"
  owner: "user"
  output_dirs:
    - deliverables
    - code
---

# Criação de Landing Pages e Sites Institucionais

## 1) Resumo executivo (skill pronta)

**Função:** gerar um **pacote completo e publicável** para **Landing Page (1 página, 1 conversão)** ou **Site Institucional (sitemap + páginas essenciais)** a partir de um **brief** — incluindo **arquitetura, copy por seção, mini design system e QA** (com opção de entregar **código** em HTML/CSS ou React/Next).

**Entradas:** brief (texto) + preferências (tipo: LP/Institucional, CTA, stack opcional, restrições de marca/legais).
**Saídas:** arquivos `.md` padronizados em `deliverables/` + opcional `code/` com implementação.
**Quando dispara:** pedidos do tipo “criar landing page”, “montar site institucional”, “gerar copy/estrutura/design+QA de site/LP”.
**Critério de sucesso:** entrega **consistente, completa e verificável** (artefatos + checklist), sem inventar dados (lacunas viram perguntas/assunções rotuladas).

## Objetivo
Produzir, a partir de um brief, um **pacote completo** para LP ou site institucional contendo:
- estratégia de mensagem (concisa),
- arquitetura (wireframe textual ou sitemap),
- copy por seção com microcopy,
- mini design system (direção + tokens + componentes),
- QA final (rubrica + top correções),
- **opcional**: implementação em código.

## Use quando
- O usuário pede “crie uma landing page” com foco em conversão (lead, agendamento, venda).
- O usuário pede “site institucional” para credibilidade (páginas, navegação, contato, SEO básico).
- O usuário quer “estrutura + copy + UI/UX + QA” em um processo previsível.
- O usuário fornece (ou aceita preencher) um brief.

## Não use quando
- O usuário quer apenas um item isolado (ex.: “só o logo”, “só um banner”, “só a paleta”).
- O usuário já tem o site pronto e quer apenas correções pequenas (ex.: trocar textos em 2 seções).
- O objetivo é e-commerce completo, área logada, backend, integrações complexas (escopo maior que marketing site).
- Requisitos legais/regulatórios complexos exigem advogado/consultoria especializada.

## Entradas esperadas
- Tipo: `LP` ou `Institucional`
- Objetivo primário e CTA primário (e secundário opcional)
- Oferta (o que é/como funciona/preço ou planos se existirem)
- Público-alvo e contexto/objeções
- Provas (depoimentos, números, clientes, credenciais)
- Diretrizes de marca (tom, referências visuais, paleta/logotipo se houver)
- Restrições (LGPD/cookies, termos, páginas obrigatórias)
- Stack (opcional): `HTML+CSS`, `Tailwind`, `React/Next`

Se faltar algo: **não inventar**. Fazer até **10 perguntas críticas** e/ou registrar **assunções rotuladas**.

## Saídas / artefatos
Criar uma pasta `deliverables/` com:
- `01_brief.md` (brief consolidado + lacunas)
- `02_mensagem.md` (promessa, diferenciais, objeções/respostas)
- `03_arquitetura.md` (LP: seções + intenção; Institucional: sitemap + intenção por página)
- `04_copy.md` (copy final por seção/página + microcopy)
- `05_design-system.md` (direções A/B + tokens + componentes)
- `06_qa.md` (rubrica 0–5 + top correções + checklist de publicação)

Opcionalmente criar `code/` com:
- `index.html` + `styles.css` (ou)
- estrutura mínima de `Next.js/React` conforme pedido do usuário.

## Workflow (passo a passo numerado)
1. **Identificar o tipo (LP vs Institucional) e o objetivo**
   - Se LP: confirmar conversão primária e CTA (sem menu ou menu mínimo).
   - Se Institucional: confirmar páginas essenciais (ex.: Home, Sobre, Serviços, Cases, Blog opcional, Contato, Privacidade).

2. **Consolidar o brief**
   - Normalizar informações (sem “encher linguiça”).
   - Listar lacunas e perguntas críticas (máx 10).
   - Registrar assunções como: `ASSUNÇÃO:`.

3. **Gerar estratégia de mensagem**
   - Promessa principal (1 frase).
   - Variações de headline (3 opções) + subheadline.
   - Diferenciais (5 bullets).
   - Objeções (5) + respostas curtas.
   - Salvar em `deliverables/02_mensagem.md`.

4. **Definir arquitetura**
   - LP: mapa de seções em ordem; para cada seção: objetivo, mensagem-chave, elementos, CTA.
   - Institucional: sitemap; para cada página: objetivo, conteúdo, CTA/ação.
   - Salvar em `deliverables/03_arquitetura.md`.

5. **Escrever copy por seção/página + microcopy**
   - Para cada seção/página: títulos, subtítulos, bullets, CTA principal + alternativa.
   - Microcopy de formulários: placeholders, ajuda, erros, confirmação.
   - Salvar em `deliverables/04_copy.md`.

6. **Direção visual + mini design system**
   - Propor Direção A e Direção B (descrição objetiva).
   - Tokens: tipografia (escala), espaçamentos, radius, sombras, grid/containers.
   - Componentes: Header, Hero, Botões, Cards, Prova social, FAQ, Footer, Form.
   - Salvar em `deliverables/05_design-system.md`.

7. **(Opcional) Implementação em código**
   - Se o usuário pediu código, escolher stack e gerar implementação mínima fiel ao conteúdo.
   - Regras: semântica, a11y (labels/focus), mobile-first, SEO básico, performance (evitar CLS).
   - Salvar em `code/`.

8. **QA final e plano de publicação**
   - Avaliar com rubrica 0–5: clareza, CTA, prova, usabilidade, mobile, a11y, performance, SEO.
   - Listar top 10 correções por impacto e checklist final.
   - Salvar em `deliverables/06_qa.md`.

## Regras e guardrails
- **Não inventar dados** (clientes, números, certificações, prazos, preços). Se faltar, perguntar ou marcar `ASSUNÇÃO`.
- **1 objetivo primário**: evitar múltiplas conversões competindo (especialmente em LP).
- **Mobile-first** sempre: hierarquia e CTA claros no primeiro scroll.
- **Acessibilidade mínima**: headings em ordem, foco visível, labels, contraste.
- **SEO básico**: title/description, H1 único, H2/H3 coerentes, conteúdo útil e escaneável.
- **Legal (Brasil) – orientação**: se houver coleta de dados/tracking, incluir página/link de Privacidade e consentimento quando aplicável (sem aconselhamento jurídico).

## Edge cases & diagnósticos
- Brief vago (“site bonito”): gerar `01_brief.md` com perguntas e **não** avançar para copy final sem hipóteses explícitas.
- Muitos CTAs: propor priorização e mapear CTA secundário por seção (sem competir com o primário).
- Sem prova social: sugerir substitutos não inventados (processo, garantias reais do cliente, transparência, bastidores).
- Oferta complexa: criar seção “Como funciona” com passos e reduzir fricção.
- Stack indefinida: entregar artefatos em `deliverables/` e perguntar se quer `HTML+CSS` ou `Next.js`.

## Exemplos
### Exemplo 1 (input → output esperado)
**Input (resumo):**
- Tipo: LP
- Objetivo: captar leads
- CTA: “Agendar diagnóstico”
- Público: donos de pequenas empresas
- Prova: 30+ projetos, 4,9/5 avaliações

**Output esperado:**
- `deliverables/01_brief.md` … `deliverables/06_qa.md`
- (Opcional) `code/index.html` + `code/styles.css`

## Teste rápido
Prompts que devem disparar:
1) “Crie uma landing page para captar leads com CTA de WhatsApp. Tenho essas infos: …”
2) “Preciso de um site institucional para meu escritório (Home, Sobre, Serviços, Contato).”
3) “Monte estrutura + copy + mini design system para uma LP de agendamento.”
4) “Gere o pacote completo (arquitetura, copy, QA) de um site institucional com SEO básico.”

Prompts que NÃO devem disparar:
1) “Crie um logo minimalista.”
2) “Revise apenas estes 2 parágrafos do meu site.”
3) “Construa um app com login e pagamentos.”

Checklist:
- Criou `deliverables/` com 6 arquivos?
- `01_brief.md` contém lacunas/perguntas e assunções rotuladas?
- LP tem 1 objetivo e CTAs coerentes? Institucional tem sitemap e intenção por página?
- `06_qa.md` traz notas 0–5 e top 10 correções?

## 2) Árvore de pastas

```text
landing-site-builder/
  SKILL.md
  agents/
    openai.yaml
  assets/
    brief-template.md
    deliverables-structure.md
  references/
    qa-rubric.md
    seo-a11y-performance.md
    implementation-notes.md
```

## 3) Arquivos completos

### // path: landing-site-builder/agents/openai.yaml

```yaml
version: 1
interface:
  display_name: "Landing & Site Builder"
  description: "Gera pacote completo (arquitetura, copy, design system e QA) para landing pages e sites institucionais."
  color: "#1F2937"
policy:
  allow_implicit_invocation: true
  recommended_invocation: "Use a skill landing-site-builder para criar LP/site institucional com entregáveis padronizados."
```

### // path: landing-site-builder/assets/brief-template.md

```md
# Brief (preencha o máximo possível)

## Essenciais
- Tipo: [LP | Institucional]
- Objetivo primário:
- CTA primário (texto exato):
- CTA secundário (opcional):
- Oferta (o que é / como funciona / planos ou preço se houver):
- Público-alvo (quem é / dores / nível de consciência):
- Contexto & mercado (concorrentes, alternativa atual, objeções comuns):

## Prova e credibilidade (somente fatos reais)
- Depoimentos:
- Avaliações:
- Números (clientes, projetos, anos, resultados):
- Certificações / credenciais:
- Garantias (se existirem):

## Marca e direção
- Tom de voz (ex.: direto, confiante, sem jargão):
- Paleta / tipografia / logo (links ou arquivos):
- Referências visuais (2–5 links):

## Conteúdo e restrições
- Seções obrigatórias (ex.: planos, portfólio, FAQ):
- Restrições legais (LGPD, termos, privacidade):
- Idioma:
- Stack desejada (opcional): [HTML+CSS | Tailwind | React/Next]

## Dados operacionais
- WhatsApp:
- Telefone:
- E-mail:
- Endereço (se aplicável):
- Horário de atendimento:
```

### // path: landing-site-builder/assets/deliverables-structure.md

```md
# Estrutura padrão de entregáveis

deliverables/
- 01_brief.md
- 02_mensagem.md
- 03_arquitetura.md
- 04_copy.md
- 05_design-system.md
- 06_qa.md

code/ (opcional)
- HTML+CSS: index.html, styles.css
- Next.js/React: estrutura mínima + componentes
```

### // path: landing-site-builder/references/qa-rubric.md

```md
# Rubrica de QA (0–5) + guia de correção

Critérios:
1) Clareza da proposta (o que é / pra quem / benefício)
2) CTA e fluxo de conversão (fricção, repetição saudável, microcopy)
3) Credibilidade (prova, transparência, sinais de confiança)
4) Usabilidade (hierarquia, escaneabilidade, consistência)
5) Mobile-first (primeiro scroll, fontes, espaçamento, cliques)
6) Acessibilidade (headings, foco, labels, contraste, alt)
7) Performance (imagens, fontes, CLS, peso)
8) SEO básico (title/description, semântica, H1 único, headings)

Formato recomendado do relatório:
- Tabela: Critério | Nota | Problema | Correção
- Top 10 mudanças por impacto (com motivo)
- Checklist final de publicação
```

### // path: landing-site-builder/references/seo-a11y-performance.md

```md
# Checklist rápido: SEO + A11y + Performance (marketing sites)

## SEO básico
- Title e meta description (coerentes com H1)
- H1 único; H2/H3 em ordem
- Seções com texto útil (não só frases vagas)
- Links descritivos (evitar “clique aqui”)
- Open Graph (título/descrição/imagem) quando houver compartilhamento

## Acessibilidade mínima
- Focus visível em links/botões/inputs
- Labels em todos os inputs (não só placeholder)
- Contraste adequado (evitar cinza muito claro)
- Alt em imagens informativas (decorativas podem ser alt="")

## Performance
- Imagens com dimensões definidas (evitar layout shift)
- Comprimir imagens e evitar formatos enormes
- Evitar fontes demais/variantes demais
- Priorizar carregamento do conteúdo acima da dobra
```

### // path: landing-site-builder/references/implementation-notes.md

```md
# Notas de implementação (quando gerar código)

Regras de ouro:
- Semântica: header/main/section/footer, botões para ações, links para navegação
- Formulário: label + aria-describedby (quando necessário) + mensagens de erro
- Mobile-first: layout fluido, container consistente, fontes legíveis
- CTA: componente reutilizável com variações (primário/secundário)

Quando usar HTML+CSS:
- Entrega rápida, simples, sem build

Quando usar React/Next:
- Se houver componentes reutilizáveis, múltiplas páginas (institucional) ou crescimento futuro
```

## 4) Instruções de instalação (repo vs usuário)

**Instalar no repositório (recomendado para time/projeto):**

- Coloque a pasta em:
  `./.agents/skills/landing-site-builder/`
  (o Codex procura `.agents/skills` do diretório atual até a raiz do repo).

**Instalar no usuário (global):**

- Coloque em:
  `$HOME/.agents/skills/landing-site-builder/`

Se não aparecer, reinicie o Codex.

## 5) Checklist de teste (prompts positivos e negativos)

### Prompts que devem disparar (implícito)

1. “Preciso criar uma **landing page** para agendar consultas. Aqui está o brief: …”
2. “Crie um **site institucional** com Home, Sobre, Serviços e Contato. Dados: …”
3. “Monte **arquitetura + copy + design system** para uma LP de venda de curso.”
4. “Quero o pacote completo e **QA** de um site institucional (SEO básico).”

### Prompt de disparo explícito

- “Use a skill **landing-site-builder**. Tipo: LP. Objetivo: captar leads. CTA: ‘Chamar no WhatsApp’. Brief: …”

### Prompts negativos (não deve disparar)

1. “Faz um logo para mim.”
2. “Só revise este parágrafo do meu site e corrija gramática.”
3. “Preciso de um app com login, banco de dados e pagamentos.”

### Checks determinísticos

- Existem **6 arquivos** em `deliverables/` com nomes padronizados?
- `01_brief.md` lista lacunas/perguntas e marca `ASSUNÇÃO:` quando necessário?
- Para LP: **1 objetivo**, CTA primário recorrente e navegação mínima?
- Para Institucional: sitemap + intenção por página em `03_arquitetura.md`?
- `06_qa.md` contém rubrica (0–5) + top 10 correções + checklist de publicação?

## 6) Notas de manutenção e versionamento

- Se o seu processo mudar (ex.: novas páginas padrão, nova stack preferida, requisitos de LGPD/cookies), atualize:
  - `SKILL.md` (workflow e guardrails)
  - `references/seo-a11y-performance.md` (checklists)
  - `references/qa-rubric.md` (critérios e formatação)
- Versione em `metadata.version` e registre mudanças relevantes no repositório (release notes/CHANGELOG do seu time).

Se quiser que essa skill **sempre gere código** (ou **nunca gere código**), dá para ajustar o `SKILL.md` para tornar isso obrigatório por padrão.
