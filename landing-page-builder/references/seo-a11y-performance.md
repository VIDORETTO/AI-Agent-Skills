# Checklists Rápidos — SEO + Acessibilidade + Performance

## SEO básico

- [ ] `<title>` único por página, < 60 caracteres, inclui palavra-chave principal
- [ ] `<meta name="description">` < 160 caracteres, orienta o clique
- [ ] H1 único por página, coerente com o `<title>`
- [ ] H2/H3 em hierarquia lógica (não pular níveis)
- [ ] Texto útil nas seções (não apenas frases decorativas)
- [ ] Links descritivos (não "clique aqui" ou "saiba mais" repetido)
- [ ] Open Graph: `og:title`, `og:description`, `og:image` (1200×630px), `og:url`
- [ ] URL amigável (ex.: `/servicos/consultoria` em vez de `/page?id=12`)
- [ ] Imagens com nome de arquivo descritivo (ex.: `consultoria-empresarial.webp`)
- [ ] Sem conteúdo duplicado entre páginas

## Acessibilidade mínima (WCAG 2.1 AA)

- [ ] Ordem de heading lógica em todo o documento
- [ ] Foco visível em todos os elementos interativos (links, botões, inputs)
- [ ] Todos os `<input>` e `<textarea>` com `<label>` associado por `for`/`id`
- [ ] Imagens informativas com `alt` descritivo; decorativas com `alt=""`
- [ ] Contraste de texto normal ≥ 4.5:1 (verificar em https://webaim.org/resources/contrastchecker/)
- [ ] Contraste de texto grande (≥ 18px bold ou ≥ 24px) ≥ 3:1
- [ ] Botões com texto descritivo (não só ícone, ou adicionar `aria-label`)
- [ ] Sem dependência exclusiva de cor para transmitir informação
- [ ] Animações respeitam `prefers-reduced-motion`
- [ ] Formulário com mensagens de erro descritivas e associadas ao campo (`aria-describedby`)

## Performance (Core Web Vitals)

### Imagens (maior causa de lentidão)
- [ ] Dimensões definidas no HTML (`width` e `height`) → evita CLS
- [ ] Hero image: `loading="eager"` + `fetchpriority="high"`
- [ ] Demais imagens: `loading="lazy"`
- [ ] Formato moderno preferido: WebP ou AVIF
- [ ] Hero comprimida < 200KB; demais < 100KB; ícones < 20KB
- [ ] Imagens responsivas com `srcset` quando necessário

### Tipografia
- [ ] Máximo de 2 famílias; máximo de 3 variantes de peso por família
- [ ] `font-display: swap` em todas as fontes externas
- [ ] `<link rel="preconnect">` para domínio da fonte

### Geral
- [ ] CSS crítico (above the fold) inline ou em `<head>` — não bloqueante
- [ ] Scripts não críticos com `defer` ou `async`
- [ ] Sem dependência de JS para renderizar conteúdo acima da dobra
- [ ] Sem redirect chains (ex.: http → https → www)

### Ferramentas de verificação
- PageSpeed Insights: https://pagespeed.web.dev/
- WebPageTest: https://www.webpagetest.org/
- Contrast Checker: https://webaim.org/resources/contrastchecker/
- Open Graph Debugger: https://opengraph.xyz/
- WAVE (acessibilidade): https://wave.webaim.org/
