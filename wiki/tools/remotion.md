---
type: tool
tags: [tools, remotion, videos, react]
title: Remotion
description: Framework React para criar vídeos programaticamente — instalado no projeto único /root/projects/remotion, com skills oficiais e referência dos projetos antigos do PC
timestamp: 2026-07-12T22:34:00-03:00
status: stable
---

# Remotion

## O que é

Framework open-source que cria **vídeos programaticamente com React**: cada vídeo é um
componente; renderização via CLI gera MP4/GIF/stills. Inclui o **Remotion Studio**, interface
web de preview que sobe sob demanda (`npx remotion studio`) — não é serviço permanente.

## Capabilities

- Composições de vídeo em React/TypeScript (animações, transições, áudio, legendas)
- Render por CLI: MP4 (HD/4K), GIF, frame estático (still)
- Studio de preview no navegador
- Ecossistema de pacotes (`@remotion/transitions`, `@remotion/media`, `@remotion/install-whisper-cpp`...)

## Limites

- Render usa bastante CPU/RAM — na VPS (2 vCPU) é mais lento que num PC
- Studio consome RAM enquanto aberto — subir só quando for usar
- Porta padrão do Studio (3000) está ocupada pelo EasyPanel na VPS; usar `--port` ou aceitar a 3001

## Como usar

- **Pasta única:** `/root/projects/remotion` — todos os vídeos são composições aqui.
  **Nunca criar outra pasta de projeto Remotion** (decisão do Giovani, 2026-07-12)
- Studio: `npx remotion studio --port 3333` na pasta; acesso do PC/celular via túnel SSH
- Render: scripts em `package.json` (`npm run build`, `build-4k`, `build-gif`, `still`)
- Skills oficiais em `.agents/skills/` (8 skills de `remotion-dev/skills`; symlinks em `.claude/skills/`)
- `referencia-pc/` — resta só `_inventario-pc.md` (listagem do que ficou no PC do Giovani:
  áudios, imagens, whisper.cpp); os `.md` de código foram apagados em 2026-07-12 após a
  reconstrução dos projetos em `src/`
- Fontes em `public/`: TheBoldFont + ClashDisplay completa, com licenças

## Quando não usar

- Vídeos simples em massa dirigidos por agente → avaliar [[wiki/tools/hyperframes.md|HyperFrames]]
- Transcrição/legendas automáticas na VPS → whisper.cpp `medium` precisa de ~2,6 GB de RAM;
  decisão de 2026-07-12: **fica no PC do Giovani** (o `.json` de legendas gerado é leve e viaja
  pra VPS quando necessário). Plano B: modelo `small` (trocar uma linha em `whisper-config.mjs`,
  código em `referencia-pc/legendaproject.md`)

## Configuração

- Instalado como dependência do projeto (não global): `remotion` 4.0.472 em
  `/root/projects/remotion/node_modules` (~687 MB)
- `remotion.config.ts` na raiz do projeto define regras globais de render

## Erros conhecidos

- Nenhum registrado até agora na VPS

## Status de validação

- **2026-07-12** — todos os projetos do PC do Giovani reconstruídos em `src/` (75 arquivos):
  25 composições listadas sem erro por `npx remotion compositions` (MotionProject, ReelsOM,
  LegendaProject, Projeto3/4/5, KaruMarketingVideo, TransitionTest + 17 Curvable-* do Projeto6).
  `package.json` adotado do PC (React 19, Remotion 4.0.469), instalação limpa
- Assets que ficaram no PC: músicas e imagens do Reels OM (composição abre com essas partes
  quebradas/mudas); `public/test2.mp4` é placeholder preto de 5s criado na VPS pro LegendaProject
- Composição antiga do ex-`remotion-video` (título/cards) saiu do `Root.tsx` (arquivos mantidos
  em `src/components/`); o id `HelloWorld` atual é do Projeto6
- Histórico: `remotion-reels` (transição de logo) excluído em 2026-07-12 a pedido do Giovani

## Conexões

- [[wiki/projects/automacao-videos.md|Automação Vídeos]] — projeto do Giovani que usa esta ferramenta
- [[wiki/tools/hyperframes.md|HyperFrames]] — ferramenta complementar HTML→vídeo
- [[wiki/systems/hermes.md|Hermes]] — stack de mídia da VPS (React + Remotion)
- [[wiki/systems/vps.md|VPS]] — limites de RAM/CPU relevantes para render e whisper
