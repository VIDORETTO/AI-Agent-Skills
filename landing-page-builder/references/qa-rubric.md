# Rubrica de QA — Landing Page / Site Institucional

## Escala de notas

| Nota | Significado |
|---|---|
| 5 | Excelente — nenhuma melhoria necessária |
| 4 | Bom — melhoria pequena possível |
| 3 | Aceitável — uma ou duas correções claras |
| 2 | Fraco — problemas que prejudicam conversão/credibilidade |
| 1 | Crítico — impede o objetivo principal |
| 0 | Ausente — o critério não foi endereçado |

---

## Critérios detalhados

### 1. Clareza da proposta (0–5)
- O visitante entende o QUE é o produto/serviço em menos de 8 segundos?
- O PARA QUEM está explícito (público-alvo identificável)?
- O BENEFÍCIO principal está na dobra (above the fold)?
- **Problema comum:** headline genérica ("Transforme seu negócio"), proposta de valor enterrada no meio da página.

### 2. CTA e fluxo de conversão (0–5)
- CTA primário aparece no Hero e se repete ao longo da página (pelo menos 3x em LP)?
- O texto do CTA é específico e orientado à ação ("Agendar diagnóstico gratuito" > "Enviar")?
- Há CTA visível a cada 2–3 scrolls?
- Microcopy reduz fricção ("Sem cartão de crédito", "Resposta em 24h")?
- Formulário tem o mínimo de campos necessário?
- **Problema comum:** CTA só no Hero e no Footer, texto genérico "Saiba mais".

### 3. Credibilidade (0–5)
- Há pelo menos 1 prova social real (depoimento, número, logo de cliente)?
- Nenhum dado inventado?
- Sinais de confiança presentes (garantia, certificação, anos de mercado)?
- Transparência sobre o processo ou produto?
- **Problema comum:** "Mais de 1.000 clientes satisfeitos" sem nome/contexto = pouco crível.

### 4. Usabilidade (0–5)
- Hierarquia visual clara (o olho sabe para onde ir)?
- Conteúdo escaneável (bullets, títulos, destaque em bold em frases-chave)?
- Consistência de tom, estilo e componentes?
- Sem parágrafos de mais de 4 linhas?
- **Problema comum:** paredes de texto, excesso de seções com mesma hierarquia visual.

### 5. Mobile-first (0–5)
- O primeiro scroll em 375px comunica a proposta completa e exibe o CTA?
- Fontes legíveis sem zoom (mínimo 16px para corpo)?
- Botões com área de toque ≥ 44px?
- Sem overflow horizontal?
- **Problema comum:** Hero com imagem que empurra CTA para fora do viewport em mobile.

### 6. Acessibilidade (0–5)
- Headings em ordem lógica (H1 → H2 → H3, sem pular)?
- Foco visível em todos os elementos interativos?
- Todos os inputs têm `<label>` (não apenas placeholder)?
- Contraste de texto ≥ 4.5:1 (normal) / 3:1 (large)?
- Imagens informativas têm `alt` descritivo?
- **Problema comum:** foco removido com `outline: none` sem alternativa, placeholders substituindo labels.

### 7. Performance (0–5)
- Imagens com dimensões definidas no HTML (evita CLS)?
- Imagens comprimidas (< 200KB para hero, < 100KB para ícones)?
- Máximo de 2 famílias de fonte, carregadas com `font-display: swap`?
- Conteúdo acima da dobra sem dependência de JS para renderizar?
- **Problema comum:** imagens de 2MB direto do Canva/Figma sem comprimir.

### 8. SEO básico (0–5)
- `<title>` único e descritivo (< 60 caracteres)?
- `<meta name="description">` relevante (< 160 caracteres)?
- H1 único por página, coerente com o title?
- Links descritivos (sem "clique aqui")?
- Open Graph definido para compartilhamento?
- **Problema comum:** title = nome da empresa sem benefício, H1 e H2 invertidos.

---

## Template do relatório de QA

```
## Resumo

| Critério            | Nota | Problema principal              | Correção sugerida             |
|---------------------|------|---------------------------------|-------------------------------|
| Clareza da proposta |      |                                 |                               |
| CTA e conversão     |      |                                 |                               |
| Credibilidade       |      |                                 |                               |
| Usabilidade         |      |                                 |                               |
| Mobile-first        |      |                                 |                               |
| Acessibilidade      |      |                                 |                               |
| Performance         |      |                                 |                               |
| SEO básico          |      |                                 |                               |

**Média geral:** X.X / 5

## Top 10 correções por impacto

1. [Critério] — [Problema] — [Correção] — Impacto: Alto/Médio/Baixo
...

## Checklist de publicação

- [ ] Title e meta description definidos para todas as páginas
- [ ] Favicon configurado
- [ ] Open Graph testado (ferramenta: opengraph.xyz)
- [ ] Formulário testado end-to-end (envio real + confirmação)
- [ ] Links internos e externos funcionando
- [ ] Imagens comprimidas e com alt
- [ ] Mobile testado em dispositivo real (ou Chrome DevTools 375px)
- [ ] Página de Privacidade / LGPD presente (se houver coleta de dados)
- [ ] Google Analytics / Pixel configurado (se solicitado)
- [ ] SSL ativo (https)
- [ ] 404 personalizada configurada
- [ ] Sitemap.xml gerado (para Institucional)
```
