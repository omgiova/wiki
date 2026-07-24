---
type: concept
tags: [libertas, seo, hugo, netlify, desenvolvimento, redacao]
title: Libertas SEO — Doc Central
description: Documento central de trabalho do projeto SEO da Libertas — setup técnico, pendências, plano de ação (código) e processo de redação dos artigos. Uso interno.
timestamp: 2026-07-24T12:00:00-03:00
status: draft
---

# Libertas SEO — Documento Central

Documento **interno de trabalho** do projeto de SEO da Libertas: reúne o setup técnico, as pendências, o plano de ação de código e o processo de redação dos artigos. Não é apresentável ao cliente — a versão para a Libertas/Luciana é o checklist [[wiki/projects/progresso-libertas-seo.md]].

> **Marco que orienta tudo:** deixar a fundação técnica impecável **antes do 1º artigo de verdade**. O post que está no ar hoje foi um **teste** e deve ser desconsiderado.

---

## 🔧 1. Setup técnico

### Repositório
- **Repo:** `git@github.com:lucianaom/libertas.git` — **privado**, dono é a conta **Luciana** (`lucianaom`).
- **Clone local (VPS):** `/root/libertas`, conectado via **SSH** (a chave da VPS já autentica como a conta **`omgiova`** do Giovani).
- **Autoria dos commits:** configurada como **Giovani `<omgiovani4@gmail.com>`** (fica no `git config` local do repo).
- **CLI:** `gh` autenticado como `omgiova` (abre PRs pelo terminal).

### Hospedagem (Netlify)
- **Projeto:** `libertasav` (team `luciana-pxox2gk's team`).
- **Build command:** `hugo --gc --minify` · **Publish dir:** `public` · **Branch de produção:** `main`.
- **Versão do Hugo:** fixada em `0.154.5` no `netlify.toml` (não precisa variável de ambiente).
- **Deploy automático:** todo merge na `main` republica sozinho em ~1–2 min.

### Stack do site
- **Hugo** (gerador de site estático) + **Tailwind CSS**. Não é React.
- Páginas saem **100% renderizadas** do servidor → Google lê `<head>`, schema, tudo. (Aquela dúvida antiga de "react-helmet" **não se aplica**.)
- Layout-mãe: `layouts/_default/baseof.html` (o que se põe aqui vale em todas as páginas).
- Artigos: arquivos `.md` em `content/blog/`. Nome do arquivo = URL.

### Rastreamento já instalado (no `<head>` do `baseof.html`)
- **GTM** `GTM-PLF52CMK` · **GA4** `G-T1YJM460H2` · **Clarity** `xr5n8y2gl4`.

### Fluxo de trabalho (obrigatório — nunca commitar direto na `main`)
1. `git checkout main && git pull`
2. `git checkout -b nome-do-galho`
3. editar → `git add -A` → `git commit -m "..."`
4. `git push -u origin nome-do-galho`
5. `gh pr create --base main --head nome-do-galho --title "..." --body "..."`
6. Netlify gera **Deploy Preview** no PR → conferir.
7. **A Luciana aprova e faz o merge pela conta dela** → produção republica.

> ⚠️ **Por que o merge é pela conta da Luciana:** o plano grátis da Netlify só permite **1 contribuidor** em repo privado. Commits assinados por Giovani fazem o **Deploy Preview** falhar (aviso "unrecognized Git contributor"), mas isso **não impede** a publicação: quando a **Luciana** faz o merge, o build de produção roda na identidade dela e publica normalmente. Se um dia quiserem previews verdes, as saídas são: tornar o repo público ou upgrade Netlify Pro.

### Regras do SOP (herdadas do dev anterior)
1. Nome do arquivo = URL → **nunca renomear** depois de publicado.
2. **Nunca** alterar a keyword principal de um post existente.
3. **Nunca** commitar direto na `main` — sempre PR.
4. **Não remover** blocos de texto de páginas existentes sem aprovação de SEO.

---

## 📌 2. Pendências

- [ ] **Marcar o clique no botão de WhatsApp como conversão (key event) no GA4** — hoje o Google Analytics mede *visita*, mas não *resultado*. Precisamos marcar o clique no botão de contato do WhatsApp como **"key event"** (a nomenclatura atual do GA4 para "conversão"). Sem isso, medimos quantas pessoas visitaram, mas não quantas de fato tentaram entrar em contato — que é o que importa para provar retorno à Libertas. Passo mais técnico; exige configurar o evento no GA4 (via GTM ou evento personalizado).

---

## 🚀 3. Plano de ação — antes do 1º artigo

Só o que falta de fato no código/configuração, repriorizado com o que já sabemos do site. Sem hipóteses.

### 3.1 Schema estruturado (JSON-LD) — **maior lacuna de código**
O site **não tem nenhum** dado estruturado hoje. Adicionar nos templates Hugo (uma vez, vale para sempre):
- **Organization** — no `<head>` da home (nome, url, logo, redes sociais).
- **BlogPosting (Article)** — no template de artigo (`single.html`): `headline`, `image`, `datePublished`, `dateModified`, `author`.
- **BreadcrumbList** — no template de artigo (Início › Blog › Título).
- ❌ **Não usar FAQPage nem HowTo** — descontinuados pelo Google (FAQ removido de vez em 05/2026). Pode manter FAQ/passo-a-passo no *texto*, só não marcar com schema.
- Validar cada tipo em `search.google.com/test/rich-results`.

### 3.2 robots.txt — **falta**
Hoje `libertasav.com.br/robots.txt` retorna 404 (não existe). Criar liberando tudo + apontando o sitemap:
```
User-agent: *
Allow: /

Sitemap: https://libertasav.com.br/sitemap.xml
```
No Hugo: `enableRobotsTXT = true` no `hugo.toml` (com template) **ou** arquivo estático em `static/robots.txt`. *(sitemap.xml já é gerado automaticamente pelo Hugo — OK.)*

### 3.3 Key event do WhatsApp (ver Pendências) — conversão no GA4.

### 3.4 Medições de baseline ("mês 0")
- Rodar `pagespeed.web.dev` e registrar Core Web Vitals (LCP, INP, CLS). Vilão provável: imagens.
- Tirar prints de Search Console e GA4 zerados + data = base do "antes × depois" dos 3 meses.

### 3.5 Decisão de marca (cliente)
- **Slug base** dos artigos: hoje o site usa **`/blog/`**. Se for manter, ok; se quiserem `/conteudo` ou `/artigos`, decidir **antes** de publicar (mudar depois quebra URLs).

> Já resolvido "de fábrica" pelo Hugo (não precisa fazer): `<title>` e meta description por página, Open Graph/Twitter, sitemap.xml, página índice `/blog`, cache/performance.

---

## ✍️ 4. Redação dos artigos

### Estratégia (do plano validado)
- **3 pilares primeiro** (artigos-guia, hub dos clusters):
  1. **Gestão Financeira Empresarial** (autoridade ampla)
  2. **BPO Financeiro** (intenção comercial, leva ao CTA de diagnóstico)
  3. **Planejamento Financeiro** (dor, caixa, previsibilidade)
- Depois, **satélites** (temas específicos) que **linkam de volta** para o pilar = topic cluster.
- Cadência: **1 artigo/semana**, ~12 em 3 meses.

### Molde de frontmatter — usar o **padrão real do site** (mais completo que o do SOP)
```yaml
---
title: "Título do post (vira o H1)"
date: 2026-08-01T09:00:00-03:00
draft: false
description: "Meta description única, ~140–160 caracteres, isca de clique"
image: "/images/nome-descritivo.webp"
tags: ["tag1", "tag2"]
categories: ["Categoria"]
author: "Libertas"
og_title: "Título para redes sociais"
og_type: "article"
og_image: "/images/nome-descritivo.webp"
twitter_card: "summary_large_image"
twitter_title: "Título Twitter"
twitter_description: "Descrição Twitter"
twitter_image: "/images/nome-descritivo.webp"
---
```

### Regras de corpo (Markdown)
- **Sem H1 no corpo** — o H1 vem do `title`. Estruturar com `##` (H2) e `###` (H3), sem pular nível.
- **Links internos** para o pilar: `[texto](/blog/slug-do-pilar/)`.
- **Imagens:** em `static/images/`, referenciar como `/images/arquivo.webp`; nome minúsculo/sem acento/com hífen; comprimir (<200 KB); **alt text** obrigatório.

### Nome do arquivo (= URL, imutável)
- minúsculo · sem acento/cedilha · sem espaço (hífen) · contém a keyword · extensão `.md`.
- Ex.: `content/blog/o-que-e-bpo-financeiro.md` → `libertasav.com.br/blog/o-que-e-bpo-financeiro/`.

### Publicação
- Seguir o **fluxo de trabalho** da Seção 1 (branch → PR → preview → merge pela Luciana).
- Antes do merge: conferir o Deploy Preview e validar o schema em `search.google.com/test/rich-results`.

---

## Conexões
- Checklist do cliente: [[wiki/projects/progresso-libertas-seo.md]]
- Plano original (hipóteses, contexto histórico): [[wiki/projects/slide-plano-de-ação.md]]
