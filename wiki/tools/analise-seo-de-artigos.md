---
type: tool
tags: [seo, artigos, auditoria, yoast, power-seo, npm, libertas]
title: Análise SEO de artigos — opções de auditoria/revisão fora do WordPress
description: Duas bibliotecas candidatas (yoastseo e @power-seo/content-analysis) para auditar, revisar e otimizar artigos de SEO na VPS, sem WordPress. Nenhuma instalada ou testada — página de decisão em aberto.
timestamp: 2026-09-08T00:00:00-03:00
status: draft
---

# Análise SEO de artigos — opções de auditoria/revisão

> ⚠️ **Nada aqui foi instalado nem testado.** Esta página registra duas candidatas levantadas em pesquisa (sessão de 2026-08-28) para o problema "como auditar um artigo de SEO aqui dentro, sem WordPress". A decisão está **em aberto**. Os números de verificações abaixo são o que a documentação das bibliotecas afirma — não foram conferidos rodando.

## O problema

O Yoast é um **revisor automático** de artigo: não escreve, lê o que foi escrito e dá nota com bolinhas verde/laranja/vermelha em duas frentes:

- **SEO** — palavra-chave no título, na URL, na meta descrição, no primeiro parágrafo e nos subtítulos; densidade da palavra-chave; tamanho do texto; links internos e externos; texto alternativo das imagens; largura do título; tamanho da meta descrição.
- **Legibilidade** — frases longas, parágrafos longos, voz passiva (recomenda no máx. 10%), palavras de transição, distribuição de subtítulos e o **Flesch Reading Ease**.

O Yoast só existe como plugin de WordPress. O setup aqui não tem WordPress (a Libertas é Hugo — ver [[project_libertas_hugo]]), então a pergunta é qual biblioteca solta faz esse papel.

## Candidata 1 — `yoastseo` (npm)

O **motor real do Yoast**, a mesma biblioteca que roda dentro do plugin, publicada aberta no npm. Faz exatamente as contas listadas acima (densidade, Flesch, voz passiva, transição, subtítulos).

- **A favor:** é literalmente o Yoast — fidelidade máxima ao que o plugin mostra.
- **Contra:** foi feita para viver dentro de um site/app, então precisa de um "embrulho" para virar comando de terminal. Dá mais trabalho de montar.
- Fonte: <https://www.npmjs.com/package/yoastseo>

## Candidata 2 — `@power-seo/content-analysis` (npm)

Biblioteca mais nova, feita justamente para análise estilo Yoast **fora do WordPress**.

Verificado no npm em 2026-09-08 (só metadados, sem instalar): autor **CyberCraft Bangladesh**, licença **MIT**, repositório <https://github.com/CyberCraftBD/power-seo>, primeira publicação em 2026-02-20, versão mais recente **1.0.19** (2026-07-27).

Segundo a documentação da versão nova, são **99 verificações** em 5 grupos:

| Grupo | Checks | O que cobre |
|---|---|---|
| SEO on-page | 31 | título, meta, palavra-chave, imagens, links |
| E-E-A-T | 23 | sinais de experiência/autoridade/confiança |
| Qualidade de conteúdo | 10 | hierarquia de H1, subtítulos, tamanho de frase/parágrafo, transição, complexidade de palavra, linguagem inclusiva |
| Intenção de busca | 27 | se o texto responde ao que a pessoa procurou |
| AEO / otimização para IA | 8 | resposta direta, FAQ, TL;DR, densidade de fatos, prontidão para citação |

### Entrada e saída (conforme o README)

Entrega-se a "ficha" do artigo — `content` (corpo em HTML, **obrigatório**) e, opcionais, `title`, `metaDescription`, `focusKeyphrase`, `slug`, `images`, `internalLinks` / `externalLinks`. Quanto mais campos, mais completa a análise.

Devolve:

- `score` / `maxScore` — nota e nota máxima (ex.: 78 de 90), convertível em porcentagem.
- `results` — cada verificação com nome, status **good / ok / poor** (as bolinhas verde/laranja/vermelho) e descrição do que fazer.
- `recommendations` — resumo só do que ficou ruim ou mediano.

### Divergência não resolvida

A versão fixa que deu para abrir na pesquisa (**1.0.15**) mostra **13 verificações** no manual, enquanto a descrição da versão mais nova fala em **99**. O número real depende da versão instalada — **precisa ser conferido rodando** antes de qualquer decisão.

- Fonte: <https://www.npmjs.com/package/@power-seo/content-analysis>
- README (99 checks): <https://app.unpkg.com/@power-seo/content-analysis@1.0.15/files/README.md>

## Como isso viraria uso no dia a dia

A ideia levantada na pesquisa foi embrulhar a escolhida numa **skill** (como a [[criar-artigo-seo-libertas-skill]] já existente), invocada por algo como `/revisar-seo <artigo>`, devolvendo nota + lista do que corrigir. Rodaria na VPS, sem site vivo e sem WordPress — funcionaria também em outro ambiente para onde o setup fosse levado.

**Não é decisão tomada** — é o formato que foi cogitado.

## Pendências antes de decidir

1. Instalar e rodar num artigo real da Libertas, ver a saída de verdade.
2. Conferir quantas verificações a versão instalada realmente traz.
3. Só então escolher entre as duas e, se for o caso, montar a skill.

## Relacionado

- [[Libertas-SEO]] — pesquisa de SEO da Libertas (menciona `jdevalk/specification.website`, do fundador do Yoast, em outro contexto)
- [[criar-artigo-seo-libertas-skill]]

---

**Origem:** sessão de 2026-08-28 (pesquisa a pedido do Giovani: "recursos mais próximos do Yoast que a gente pode ter aqui dentro"). Registrada em 2026-09-08 a pedido dele, explicitamente **sem validação**.
