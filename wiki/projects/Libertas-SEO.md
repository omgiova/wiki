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
- [ ] **Medição de baseline ("mês 0")** — antes do 1º artigo, registrar o ponto de partida para o comparativo "antes × depois" de 3 meses: rodar `pagespeed.web.dev` e anotar os Core Web Vitals (LCP, INP, CLS), e tirar prints de Search Console e GA4 ainda zerados, com data.

---

## 🚀 3. Plano de ação — antes do 1º artigo

> 🚧 **RASCUNHO** — seção em construção, ainda não validada pelo Giovani. Não usar como fonte definitiva.

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

> Já resolvido — **não fazer:** slug base já é `/blog/` (correto). `<title>` e meta description por página, Open Graph/Twitter, sitemap.xml, página índice `/blog`, cache/performance — tudo automático de fábrica pelo Hugo.

---

## ✍️ 4. Redação dos artigos

> 🚧 **RASCUNHO** — seção em construção, ainda não validada pelo Giovani. Não usar como fonte definitiva.

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

## 📚 5. Sessão de pesquisa e embasamento — 30-07-2026

Sessão dedicada a trocar o "mínimo obrigatório" por embasamento em **fonte primária** (Google Search Central, web.dev, schema.org). Resultado: 8 documentos novos em `/root/Libertas-SEO/` (não versionados na wiki ainda — aguardam validação do Giovani). Três achados mudaram o plano. **(1)** Conteúdo financeiro é **YMYL**, a categoria que o Google avalia com o padrão mais rigoroso — logo `author: "Libertas"` é insuficiente: exige autor pessoa física com credencial e página de autor. Definido: **Thairine Santana**, fundadora, assina os artigos. **(2)** O Google publicou um guia oficial de IA generativa que **nega** a existência de otimização especial para AI Overviews — sem schema especial, sem `llms.txt`, sem "chunking", sem escrita diferenciada; a única alavanca de conteúdo que ele destaca é **ponto de vista único / não-commodity**. O `llms.txt` foi descartado (Google não suporta; Ahrefs mediu 97% dos arquivos nunca lidos). **(3)** Verificação ao vivo do site achou lacunas não mapeadas: `rel=canonical` ausente no site inteiro, robots.txt 404, zero JSON-LD, 6 das 10 URLs do sitemap sendo taxonomias vazias, meta description da home com 196 caracteres (truncada) e imagem social em `.svg` (inválida). Novidade acionável: o Search Console tem **relatório de desempenho de IA generativa** — dá para medir visibilidade em IA de graça, entra na baseline do "mês 0".

Também foram avaliados **15 conjuntos de skills/ferramentas** de repositórios distintos, com teste objetivo (trata FAQ/HowTo como rich result vivo? cita o guia oficial? exige API paga? quem é o autor?). Dado central: **só 3 dos 15 tratam corretamente a deprecação do FAQ**, ocorrida em 07/05/2026 — por isso a documentação em `/root/Libertas-SEO/` permanece a autoridade, e skill é só ferramenta. Selecionadas 4: `jdevalk/specification.website` (do fundador do Yoast, especificação técnica com fonte citada), `coreyhaines31/marketingskills` (42k★, conteúdo/copy/CRO — não usar para schema), `AgriciDaniel/claude-seo` (melhor em SEO/AEO) e `iannuttall/seo` (medição). Confirmado que `SKILL.md` é **padrão aberto** desde 18/12/2025, rodando em ~40 produtos — não há lock-in no Claude. **Nada foi instalado e nenhuma linha do site foi alterada** — a sessão foi só de pesquisa. Definido também que SEO **local** não se aplica (serviço remoto para o Brasil todo): sem `LocalBusiness`, endereço ou Google Business Profile.

## 📖 6. Consolidação do embasamento em guia único — 31-07-2026

Os 8 documentos da sessão de pesquisa estavam organizados **por tema**, o que servia para consultar mas não para decidir: não davam, por item, o porquê nem o peso relativo de cada tarefa. Criado `/root/Libertas-SEO/GUIA-DECISAO.md`, que consolida todos eles **por item de trabalho**, ordenado por impacto, com estrutura fixa para cada um: o que é, por que existe, se ajuda em SEO/AEO/GEO, impacto real, o que acontece se não fizer, como está hoje no código, como corrigir, gotchas e fonte. O estado do código foi reverificado ao vivo em `/root/libertas` em 31-07-2026 — confirmadas as lacunas já mapeadas (canonical ausente, robots.txt 404, zero JSON-LD, taxonomias indexáveis, post de teste com `.svg` em 4 campos) e encontrado um detalhe novo: o bloco `[outputs]` do `hugo.toml` está declarado como `home = ["HTML"]`, o que **impede o Hugo de gerar o robots.txt mesmo com `enableRobotsTXT = true`** — é preciso incluir `robotsTXT` na lista.

Três definições conceituais que o guia fixa. **(1)** SEO, AEO e GEO não são três trabalhos: o Google afirma que não há requisito adicional nem schema especial para aparecer em AI Overviews, então o que existe são três funções — ser indexado certo, ser descrito com precisão e ser digno de citação — e só a terceira gera vantagem. **(2)** Dados estruturados **não** são alavanca de AEO/GEO (o guia oficial nega explicitamente); servem a rich results e desambiguação de entidade, que é SEO clássico. **(3)** Introduzida escala de impacto honesta — alto / médio / higiene — em que canonical, robots.txt e breadcrumb aparecem como **higiene**: a ausência prejudica, a presença não gera ganho. O breadcrumb, em particular, só está na lista porque o `BreadcrumbList` exige elemento visível na página, não por mérito próprio.

Também foi corrigida uma afirmação não verificada que circulava no `fundacao-tecnica.md`: a justificativa de que o canonical importa porque "a Libertas roda tráfego pago". **A Libertas não roda tráfego pago** — a frase era suposição de negócio inserida entre itens de diagnóstico técnico. Pendente decidir se a linha é removida daquele documento.

Entregue também em PDF (`Guia-Decisao-Libertas-SEO.pdf`, 20 páginas), gerado por script próprio em Python puro (`.build-pdf.py`), sem instalar nenhum pacote na VPS, com conferidor de sobreposição de texto (`.check-pdf.py`). **Nenhuma linha do site foi alterada nesta sessão** — segue tudo aguardando aprovação do guia antes de abrir o branch da fundação técnica.

## Conexões
- Guia consolidado por item (fora da wiki): `/root/Libertas-SEO/GUIA-DECISAO.md`
- Checklist do cliente: [[wiki/projects/progresso-libertas-seo.md]]
- Plano original (hipóteses, contexto histórico): [[wiki/projects/slide-plano-de-ação.md]]
- Processo de produção de artigo (skill do Claude Code): [[wiki/tools/criar-artigo-seo-libertas-skill.md]]
