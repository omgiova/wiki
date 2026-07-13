---
type: tool
tags: [tools, hyperframes, videos, html, heygen]
title: HyperFrames
description: CLI open-source da HeyGen que renderiza HTML/CSS/JS em vídeo MP4 — feita para agentes de IA; instalada globalmente na VPS, ainda sem uso real
timestamp: 2026-07-12T22:34:00-03:00
status: draft
---

# HyperFrames

## O que é

Ferramenta **open-source da HeyGen** (Apache 2.0, sem créditos ou limites) que transforma
**HTML/CSS/JS em vídeo MP4**: renderiza cada frame num Chrome headless e codifica com FFmpeg.
Determinística (mesma entrada → mesmo vídeo). Desenhada para que **agentes de IA escrevam o
código do vídeo e rendam sozinhos**.

## Capabilities

- HTML/CSS/JS → MP4; animações via CSS, GSAP, Lottie, shaders, Three.js
- Ideal para volume: dados/planilhas → vídeos, artigos → reels, template → variações personalizadas
- Skill oficial para agentes: `npx skills add heygen-com/hyperframes` (ainda não instalada)

## Limites

- Menos poder de composição fina que o Remotion para vídeos elaborados
- Render na VPS compete por CPU (2 vCPU)

## Como usar

- CLI global: `hyperframes` (help embutido)
- Ainda sem workflow definido — aguardando definição do projeto [[wiki/projects/automacao-remotion.md|Automação Remotion]]

## Quando não usar

- Vídeos com lógica React complexa e componentes já prontos → usar [[wiki/tools/remotion.md|Remotion]]

## Configuração

- Instalada em 2026-07-12: CLI global v0.7.55 (`npm install -g hyperframes`)
- Chrome Headless Shell v152 em `~/.cache/hyperframes` (~262 MB), baixado via `hyperframes browser ensure`
- Requisitos na VPS: Node 22 ✓, FFmpeg 6.1 ✓, `unzip` (instalado em 2026-07-12 para o download do Chrome)

## Erros conhecidos

- `hyperframes browser ensure` falha com "no zip archiver is available" se o sistema não tiver
  `unzip` — resolvido instalando `unzip` via apt

## Status de validação

- Instalada e "Ready to render" ✓ — **nenhum vídeo real renderizado ainda**

## Conexões

- [[wiki/projects/automacao-remotion.md|Automação Remotion]] — projeto que motivou a instalação
- [[wiki/tools/remotion.md|Remotion]] — ferramenta complementar para vídeos elaborados
