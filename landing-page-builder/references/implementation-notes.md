# Notas de Implementação — Código (HTML+CSS ou React/Next)

## Quando usar cada stack

| Stack | Usar quando |
|---|---|
| HTML + CSS puro | Entrega rápida, sem build, sem JS dinâmico necessário, 1 página |
| HTML + Tailwind CDN | Como acima, mas o cliente quer utilitários CSS |
| React (Vite) | Componentes reutilizáveis, estado dinâmico (filtros, tabs, modal) |
| Next.js | Múltiplas páginas, SEO crítico, CMS headless, ISR |

---

## Estrutura de arquivos mínima

### HTML + CSS
```
code/
  index.html
  styles.css
  assets/
    images/   (placeholder ou reais)
    fonts/    (se não usar Google Fonts CDN)
```

### React / Next.js
```
code/
  src/
    components/
      Header.jsx
      Hero.jsx
      [SectionName].jsx
      Footer.jsx
    pages/ (ou app/ para Next 13+)
      index.jsx
    styles/
      globals.css
  package.json
  README.md  (instruções de instalação e deploy)
```

---

## Regras obrigatórias

### Semântica HTML
- `<header>` para cabeçalho global, `<nav>` para navegação, `<main>` para conteúdo principal
- `<section>` com `aria-label` ou `aria-labelledby` para seções sem heading próprio
- `<footer>` para rodapé
- `<button>` para ações (não `<div onclick>` ou `<a>` sem href)
- `<a>` para navegação/links externos

### Formulários
```html
<!-- Correto -->
<label for="email">E-mail</label>
<input type="email" id="email" name="email" aria-describedby="email-hint" required>
<span id="email-hint">Usaremos apenas para enviar sua proposta.</span>

<!-- Errado -->
<input type="email" placeholder="Digite seu e-mail">
```

### Acessibilidade mínima
- `outline` nunca removido sem substituto visual
- Imagens decorativas: `alt=""`; imagens informativas: `alt="[descrição concisa]"`
- Contraste: usar https://webaim.org/resources/contrastchecker/
- Animações: respeitar `prefers-reduced-motion`

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### Mobile-first CSS
```css
/* Base: mobile (375px) */
.hero { padding: 2rem 1rem; }

/* Tablet */
@media (min-width: 768px) {
  .hero { padding: 4rem 2rem; }
}

/* Desktop */
@media (min-width: 1200px) {
  .hero { padding: 6rem 0; }
}
```

### SEO
```html
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Proposta de valor principal] | [Nome da empresa]</title>
  <meta name="description" content="[Benefício principal] para [público]. [CTA ou diferencial]. ">
  <!-- Open Graph -->
  <meta property="og:title" content="[Título]">
  <meta property="og:description" content="[Descrição]">
  <meta property="og:image" content="[URL da imagem 1200x630]">
  <meta property="og:url" content="[URL canônica]">
</head>
```

### Performance
- Imagens: sempre definir `width` e `height` para evitar CLS
- Hero image: `loading="eager"` + `fetchpriority="high"`; demais: `loading="lazy"`
- Fontes: `<link rel="preconnect">` + `font-display: swap`
- Evitar mais de 2 famílias tipográficas

```html
<!-- Carregamento de fonte otimizado -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
```

---

## Componente CTA reutilizável (exemplo HTML)

```html
<!-- Variante primária -->
<a href="#contato" class="btn btn--primary">Agendar diagnóstico gratuito</a>

<!-- Variante secundária -->
<a href="#como-funciona" class="btn btn--secondary">Ver como funciona</a>
```

```css
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  min-height: 44px;       /* área de toque mínima */
  padding: 0.75rem 1.5rem;
  border-radius: 0.5rem;
  font-weight: 600;
  text-decoration: none;
  transition: background 0.2s, transform 0.1s;
  cursor: pointer;
}

.btn:focus-visible {
  outline: 3px solid var(--color-accent);
  outline-offset: 2px;
}

.btn--primary {
  background: var(--color-primary);
  color: #fff;
}

.btn--primary:hover {
  background: var(--color-primary-dark);
}
```

---

## README mínimo para entrega de código

```md
# [Nome do projeto] — [Tipo: LP | Site Institucional]

## Instalação

### HTML puro
Abrir `index.html` no navegador ou subir em qualquer hosting estático.

### Next.js
\`\`\`bash
npm install
npm run dev   # desenvolvimento
npm run build # produção
\`\`\`

## Deploy recomendado
- **Estático:** Netlify, Vercel, GitHub Pages, Cloudflare Pages
- **Next.js:** Vercel (zero-config)

## Personalização
- Cores: editar variáveis CSS em `styles/globals.css` (`:root { ... }`)
- Conteúdo: textos estão em `[arquivo de conteúdo ou componente específico]`
- Imagens: substituir arquivos em `assets/images/` mantendo os mesmos nomes
```
