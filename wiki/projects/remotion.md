---
type: concept
tags: [projects, remotion, hyperframes, videos, automacao]
title: Remotion — automação de vídeos
description: Projeto único de vídeos em /root/projects/remotion (Remotion + HyperFrames instalado); referência dos projetos antigos do PC em referencia-pc/
timestamp: 2026-07-12T22:34:00-03:00
status: draft
---

# Remotion — automação de vídeos

Projeto de **automação de vídeos** do Giovani, iniciado em 2026-07-12. Objetivo em definição
(reels/shorts, vídeos longos, exploração) com meta de automação **totalmente automática**.

## Localização e estrutura

| O quê | Onde |
|---|---|
| Projeto único | `/root/projects/remotion` — todos os vídeos são composições aqui (nunca criar outra pasta de projeto Remotion) |
| Skills oficiais | `.agents/skills/` (8 skills de `remotion-dev/skills`, symlinks em `.claude/skills/`) |
| Referência do PC | `referencia-pc/` — código verbatim dos projetos antigos do PC, um `.md` por projeto; ver o `README.md` da pasta (índice) |
| Fontes | `public/` — TheBoldFont + ClashDisplay completa, com licenças |

## Ferramentas

- **Remotion** — instalado nas dependências do projeto (não global)
- **HyperFrames** (HeyGen, open-source) — CLI global v0.7.55 + Chrome Headless Shell em
  `~/.cache/hyperframes` (~262 MB); renderiza HTML→MP4, requisitos (Node 22+, FFmpeg, unzip) OK
  na VPS. Ainda sem uso real — instalado para avaliação

## Decisões

- **2026-07-12** — transcrição whisper.cpp (legendas automáticas) **fica no PC do Giovani**:
  modelo `medium` precisa de ~2,6 GB de RAM e a VPS (2 vCPU, ~3 GB livres) ficaria lenta/arriscada.
  O PC gera o `.json` de legendas (leve) e ele viaja pra VPS se precisar. Plano B: modelo `small`
  na VPS (trocar uma linha em `whisper-config.mjs` — código em `referencia-pc/legendaproject.md`)
- **2026-07-12** — projeto `remotion-reels` (transição de logo) excluído a pedido do Giovani;
  `remotion-video` renomeado para `remotion`

## Conexões

- [[wiki/systems/hermes.md|Hermes]] — stack de mídia da VPS (React + Remotion)
- [[wiki/systems/vps.md|VPS]] — recursos de RAM/CPU que limitam o whisper local
