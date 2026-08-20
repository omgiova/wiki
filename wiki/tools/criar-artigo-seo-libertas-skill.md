---
type: tool
tags: [tools, skill, seo, libertas, claude-code, redacao]
title: Skill criar-artigo-seo-libertas
description: Skill do Claude Code que padroniza a produção de artigos de SEO da Libertas do zero à entrega validável (HTML autossuficiente + docx); viva, refinada por changelog (v1.1.0 em 2026-08-19)
timestamp: 2026-08-19T20:00:00-03:00
status: draft
---

# Skill `criar-artigo-seo-libertas`

## O que é
Skill do Claude Code que documenta o **processo objetivo de criar um artigo de SEO da Libertas** (site Hugo em `/root/libertas`), do zero até a entrega validável. Nasceu da produção dos pilares **Planejamento Financeiro** e **Gestão Financeira** e concentra o que se aprendeu para não repetir os erros que tornaram o primeiro artigo lento.

É uma **skill viva**: cada refinamento vira uma entrada de changelog dentro do próprio `SKILL.md`.

## Onde fica
- `/root/.claude/skills/criar-artigo-seo-libertas/SKILL.md` (fonte da verdade).
- Invocável no Claude Code quando o pedido é escrever/refazer um artigo do blog da Libertas.

## O que ela cobre (resumo)
Fluxo em 9 passos: **palavra-chave e intenção → ângulo original (vem do usuário, nunca inventado) → esqueleto aprovado antes de escrever → escrita → elemento interativo → imagens → preview validável → entregas → publicação.**

Regras-chave fixadas:
- **Do zero, sem copiar** os outros artigos (texto, exemplos, analogias, stats).
- **Ângulo original vem do usuário** (caso real, número próprio, erro observado); nunca fabricar nem apresentar dedução como fato/observação da Libertas.
- **Resposta-primeiro**; **H2 em forma de pergunta** na linguagem do cliente.
- **GEO/AEO sem truque:** não há schema especial, `llms.txt` nem chunking; alavancas reais = resposta-primeiro, seções auto-contidas, fatos com fonte, entidades claras e **ponto de vista original** (a única vantagem de citação por IA).
- **Sem travessão** no corpo; blocos curtos; cards em vez de tabelas longas.
- **YMYL:** autoria pessoa real (Thairine Santana), fontes reais e verificadas.
- **≥ 1 elemento interativo/diferenciado** por artigo, melhor que o anterior (calculadora, gráfico interativo, SVG original).
- **Imagens:** prompts detalhados/realistas em inglês (texto na imagem = especificar português), conceitos diversos, e **slug + alt + legenda** corretos e distintos (alt é metadado, legenda é visível e usa a keyword).
- **Preview:** HTML autossuficiente (CSS embutido + imagens base64), placeholders para imagens pendentes. **DOCX** quando pedido (md→HTML→docx; interativo vira print).

## Armadilhas técnicas registradas na skill
- HTML autossuficiente precisa tratar `src` com e sem aspas (shortcode `figure` do Hugo minifica sem aspas) e trocar `/.netlify/images?url=...`.
- md→docx direto descarta tabelas em HTML cru: ir **md→HTML→docx**.
- Contagem de palavras/tabelas: XML numa linha só quebra `grep -c`; usar `grep -o | wc -l`.

## Estado
- Versão atual: **v1.1.0** (2026-08-19). Changelog completo no `SKILL.md`.
- Melhorias previstas: reduzir rodadas de imagem, componente de calculadora/gráfico reutilizável, script único para gerar HTML autossuficiente + docx.

## Conexões
- Doc central do projeto: [[wiki/projects/Libertas-SEO.md]]
- Embasamento por item (fora da wiki): `/root/Libertas-SEO/GUIA-DECISAO.md`
