# Libertas — Plano de Ação SEO (Fase 0 + arquitetura)

> Documento de trabalho para retomar em nova sessão. Consolida as decisões e o guia técnico da Fase 0 (fundação antes do 1º artigo). Base: `/root/SEO-ppt/` (benchmarking + slides v2, 21 slides) e `/root/Libertas/guia-libertas.md`.
> **Status:** rascunho de execução, ainda não 100% validado pelo cliente. Última atualização: 2026-07-20 (BRT).

---

## Contexto rápido

- **Cliente:** Libertas Assistência Virtual — assessoria de gestão financeira **remota** para empresas de médio/grande porte. Tom: próximo, acessível, descomplicado, sem economês.
- **Site:** `libertasav.com.br` (também responde `.com`). Feito em vibe code → **React**, hospedado no **Netlify**.
  - Conteúdo **vem renderizado** (não é SPA-casca-vazia) → Google consegue ler. Não precisa migrar de plataforma.
  - **Falta tudo de SEO:** sem `<title>`, sem meta description, sem schema. É exatamente a Fase 0.
  - **Dúvida em aberto (precisa do código):** o schema/`<head>` chega renderizado pro Google? No React normalmente exige `react-helmet` ou equivalente. Confirmar no repositório.
  - A home já abre no tom certo: *"Seu negócio fatura bem, mas o lucro some no fim do mês?"*

## Decisões tomadas

- **Cadência:** 1 artigo/semana (~12 em 3 meses). 3 páginas-pilar publicadas já no mês 1 (servem de destino dos links internos).
- **Arquitetura de URL:** URLs **planas** (não aninhar por pilar). Clusters se formam por **link interno**, não por pasta.
  - Base oficial (Google): palavra-chave no caminho da URL *"[has] hardly any effect beyond appearing in breadcrumbs"*; pasta só ajuda a entender frequência de atualização, não ranking; *"do whatever makes sense for your business."*
- **Slug base:** decisão do CLIENTE, entre **blog / artigos / conteudo**. Nenhuma muda ranking (escolha de marca). Recomendação do Claude: `/conteudo` (mais flexível), `/artigos` em 2º.
- **Página índice:** `/[slug-base]` lista todos os artigos agrupados por pilar. Não substitui os pilares (que são 3 artigos-guia).

## Conceito de "página pilar" (para retomar contexto)

Página pilar = **um artigo**, o guia mais completo de um tema (não é uma pasta que contém artigos). Os satélites (artigos específicos) **linkam de volta** para o pilar = topic cluster. Os 3 pilares:
- **P1 · Gestão Financeira Empresarial** — autoridade ampla, hub central.
- **P2 · BPO Financeiro** — intenção comercial, leva CTA de diagnóstico.
- **P3 · Planejamento Financeiro** — dor, caixa, previsibilidade.

---

## ⚠️ Correção crítica aos slides (v2, slide 20)

Os slides recomendam **FAQPage** (⭐⭐ Alta) e **HowTo** (⭐ Média). **Ambos foram descontinuados pelo Google** e não geram mais resultado enriquecido:
- **HowTo:** *"this rich result is no longer shown in search results, on both desktop and mobile devices"* (set/2023).
- **FAQPage:** restringido a governo/saúde em 2023 e **removido de vez em 08/05/2026**.

**Consequência:** pode-se manter FAQ e passo-a-passo no **texto** do artigo (ajuda leitor/conteúdo), mas **não marcar** com schema FAQPage/HowTo. Schema que sobra e funciona: **Article, BreadcrumbList, Organization, LocalBusiness**.

---

## GUIA TÉCNICO — Fase 0 (antes do 1º artigo)

Cada etapa fundamentada na documentação oficial do Google (frases-fonte entre aspas).

### Etapa 1 — robots.txt
- Controla **rastreamento**, não indexação: *"manages crawling access, not indexing itself."* (não serve pra esconder página — pra isso é `noindex`).
- Localização rígida: *"must be located at the root of the site host"* → `libertasav.com.br/robots.txt`.
- Diretiva `Sitemap:` exige URL absoluta: *"The sitemap URL must be a fully-qualified URL."*
- Conteúdo (site novo, liberar tudo):
```
User-agent: *
Allow: /

Sitemap: https://libertasav.com.br/sitemap.xml
```
- No Netlify/React: arquivo estático em `public/robots.txt`.

### Etapa 2 — sitemap.xml
- Limite (verbatim): *"All formats limit a single sitemap to 50MB (uncompressed) or 50,000 URLs."* (temos ~15 URLs).
- `<loc>` obrigatório com URL absoluta: *"Use fully-qualified, absolute URLs."*
- `<lastmod>` só se preciso e real: *"if it's consistently and verifiably accurate."*
- `<priority>` e `<changefreq>`: *"Google ignores"* — não usar.
- Encoding UTF-8. Ideal: gerar automático no build do Netlify (script varre as rotas).
```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url><loc>https://libertasav.com.br/</loc></url>
  <url><loc>https://libertasav.com.br/[slug-base]</loc></url>
  <url>
    <loc>https://libertasav.com.br/[slug-base]/o-que-e-gestao-financeira-empresarial</loc>
    <lastmod>2026-07-20</lastmod>
  </url>
</urlset>
```

### Etapa 3 — Google Search Console
- Ferramenta nº1 de medição (buscas, posição, cliques, indexação).
- Tipo de propriedade: usar **Domain property** (`libertasav.com.br`) — cobre www/não-www/http/https: *"Verifying ownership of a root domain automatically verifies ownership of all subdomains."* Exige verificação por **DNS (registro TXT)**.
- Passos: adicionar propriedade Domínio → colar TXT no painel DNS do registrador → Verificar → Sitemaps → enviar `sitemap.xml`.
- Plano B (se DNS demorar): URL-prefix com tag HTML no `<head>` (cobre só uma versão).

### Etapa 4 — Bing Webmaster Tools
- `bing.com/webmasters` → **Importar do Google Search Console** (puxa propriedade + sitemap). ~2 min. Alimenta Bing/Copilot/IAs.

### Etapa 5 — Google Analytics 4
- Mede comportamento pós-clique (tempo, páginas, conversão).
- Criar propriedade GA4 → fluxo de dados Web → tag `gtag.js` no `<head>` (ou via GTM).
- **Conversão** = agora "key event" (nomenclatura 2024). Admin → Eventos → marcar como key event o evento de resultado (ex.: clique WhatsApp / diagnóstico). Sem isso mede visita, não resultado.
- GA4 também pode verificar o Search Console.

### Etapa 6 — Schema fixo da home (1x)
- `<script type="application/ld+json">` no `<head>`. Organization (+ LocalBusiness só se houver endereço/atuação regional — serviço é remoto, pode omitir). Não foram descontinuados.
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Libertas Assistência Virtual",
  "url": "https://libertasav.com.br",
  "logo": "https://libertasav.com.br/logo.png",
  "sameAs": ["https://instagram.com/...", "https://linkedin.com/company/..."]
}
</script>
```

### Etapa 7 — Molde de cada artigo (1x, reutilizado)
Quatro coisas no `<head>` de todo artigo:

**7.1 `<title>`** — *"every page... has a title"*, *"descriptive and concise"*, marca separada por delimitador. Padrão: `KW principal: complemento | Libertas`. Sem limite duro, mas ~60 caracteres (trunca por largura de tela). Ex.: `BPO Financeiro: o que é e como funciona | Libertas`.

**7.2 Meta description** — Google *"sometimes uses"* (não sempre); **única por página** (*"Identical or similar descriptions... aren't helpful"*); não é ranking, é isca de clique; ~140–160 caracteres.

**7.3 Article schema** — tipos válidos `Article`/`NewsArticle`/`BlogPosting`; *"There are no required properties; instead, add the properties that apply."* Recomendadas: `headline`, `image`, `datePublished`, `dateModified`, `author`.
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": "O que é BPO Financeiro: como funciona e para quem vale",
  "image": "https://libertasav.com.br/img/bpo.jpg",
  "datePublished": "2026-08-01T09:00:00-03:00",
  "dateModified": "2026-08-01T09:00:00-03:00",
  "author": {"@type":"Organization","name":"Libertas Assistência Virtual","url":"https://libertasav.com.br"}
}
</script>
```

**7.4 BreadcrumbList** — `itemListElement` com `name`+`position`; `item` obrigatório menos no último (*"the last breadcrumb item may omit item since Google uses the URL of the containing page"*).
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    {"@type":"ListItem","position":1,"name":"Início","item":"https://libertasav.com.br/"},
    {"@type":"ListItem","position":2,"name":"Conteúdo","item":"https://libertasav.com.br/[slug-base]"},
    {"@type":"ListItem","position":3,"name":"O que é BPO Financeiro"}
  ]
}
</script>
```
Validar todo artigo em `search.google.com/test/rich-results` antes de publicar.
Nota React: `<head>` dinâmico normalmente exige `react-helmet` — confirmar no código que o schema chega renderizado pro Google.

### Etapa 8 — Página índice `/[slug-base]`
Vitrine que lista artigos por pilar. Pode subir antes do 1º artigo. Entra no sitemap e é nível 2 do breadcrumb.

### Etapa 9 — Core Web Vitals (velocidade)
Rodar `pagespeed.web.dev`. Metas "bom" (percentil 75):
| Métrica | Mede | Meta |
|---|---|---|
| LCP | carregamento | *"within 2.5 seconds"* |
| INP (substituiu FID em 2024) | resposta à interação | *"200 milliseconds or less"* |
| CLS | estabilidade visual | *"0.1 or less"* |
Vilão comum no vibe code: imagens pesadas.

### Etapa 10 — Registrar "mês 0"
Antes de publicar, Search Console e GA4 estão em zero. Tirar print + data = baseline pro "antes × depois" da análise de 3 meses.

---

## Divisão de mãos

| Dá pra fazer hoje (conta Google + DNS) | Precisa de acesso ao código React/Netlify |
|---|---|
| Etapa 3 (Search Console+DNS), 4 (Bing), 9 (PageSpeed), 10 (baseline) | Etapa 1 (robots), 2 (sitemap), 5 (tag GA4), 6 (schema home), 7 (molde artigo), 8 (índice) |

**Gargalo:** acesso ao código (6 de 10 etapas + confirma a dúvida da renderização do schema).

---

## Próximos passos (retomar aqui)

1. Cliente decide slug base (blog/artigos/conteudo).
2. Conseguir acesso ao **código/repositório** do site (destrava 6 etapas + dúvida de renderização).
3. Fase 1 — produção: começar pelo **briefing do 1º pilar** (P1 · gestão financeira). Calendário dos 12 artigos e medição da Fase 2 estão no plano consolidado (histórico da sessão / material em `/root/SEO-ppt/`).

## Referências
- Material-fonte: `/root/SEO-ppt/benchmarking-libertas-completo.md`, `/root/SEO-ppt/conteudo-slides.md`, apresentação v2 (21 slides).
- Guia de marca: `/root/Libertas/guia-libertas.md` e `/root/wiki/raw/libertas.md`.
- Docs Google citadas: url-structure, seo-starter-guide, structured-data/article · breadcrumb · faqpage · how-to, sitemaps/build-sitemap, robots/create-robots-txt, appearance/title-link · snippet, web.dev/vitals, Search Console verification, GA4 key events.
