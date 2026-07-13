---
type: concept
tags: [projects, automacao-videos, videos, remotion, hyperframes]
title: Automação Vídeos
description: Projeto do Giovani de automação de vídeos (Remotion e/ou HyperFrames) — em definição; meta é geração totalmente automática
timestamp: 2026-07-12T23:05:00-03:00
status: draft
---

# Automação Vídeos

Projeto de **automação de vídeos** do Giovani, iniciado em 2026-07-12. **Ainda em definição** —
o Giovani tem ideias em mente que ainda não foram detalhadas; esta página registra só o que já
foi decidido.

## O que já está definido

- **Meta:** geração de vídeos **totalmente automática** (um gatilho gera o vídeo pronto)
- **Formatos em exploração:** reels/shorts verticais e vídeos longos de YouTube
- **Ferramentas candidatas:** [[wiki/tools/remotion.md|Remotion]] (vídeos elaborados) e
  [[wiki/tools/hyperframes.md|HyperFrames]] (volume dirigido por agente) — podem coexistir
- **Fonte de conteúdo, narração/áudio:** ainda não decididos

## Base já preparada (2026-07-12)

- Projeto único do Remotion consolidado em `/root/projects/remotion` (detalhes na página da ferramenta)
- HyperFrames instalado na VPS, pronto pra renderizar
- Conhecimento dos projetos antigos do PC extraído para `referencia-pc/` (legendas automáticas
  com whisper, biblioteca visual do Projeto6, template Reels OM etc.)
- Decisão: transcrição whisper.cpp fica no PC do Giovani (RAM/CPU da VPS insuficientes pro modelo `medium`)

## Conexões

- [[wiki/tools/remotion.md|Remotion]]
- [[wiki/tools/hyperframes.md|HyperFrames]]
