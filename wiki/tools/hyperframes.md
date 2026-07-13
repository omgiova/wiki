---
type: tool
tags: [tools, hyperframes, videos, html, heygen]
title: HyperFrames
description: CLI open-source da HeyGen que renderiza HTML/CSS/JS em vídeo MP4 — feita para agentes de IA; instalada globalmente na VPS, ainda sem uso real
timestamp: 2026-07-12T23:45:00-03:00
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
- Skills oficiais para agentes: instaladas em 2026-07-12 via `npx skills add heygen-com/hyperframes` — 20 skills (core, CLI, animação, keyframes, e casos de uso como `website-to-video`, `slideshow`, `faceless-explainer`, `music-to-video`, `remotion-to-hyperframes`)

## Limites

- Menos poder de composição fina que o Remotion para vídeos elaborados
- Render na VPS compete por CPU (2 vCPU)

## Como usar

- CLI global: `hyperframes` (help embutido)
- Projetos de vídeo centralizados em `/root/projects/hyperframes`
- Skills disponíveis em qualquer sessão Claude iniciada no `/root` (ver Configuração)
- Ainda sem workflow definido — aguardando definição do projeto [[wiki/projects/automacao-remotion.md|Automação Remotion]]

## Quando não usar

- Vídeos com lógica React complexa e componentes já prontos → usar [[wiki/tools/remotion.md|Remotion]]

## Configuração

- Instalada em 2026-07-12: CLI global v0.7.55 (`npm install -g hyperframes`)
- Chrome Headless Shell v152 em `~/.cache/hyperframes` (~262 MB), baixado via `hyperframes browser ensure`
- Requisitos na VPS: Node 22 ✓, FFmpeg 6.1 ✓, `unzip` (instalado em 2026-07-12 para o download do Chrome)
- Pasta de projetos: `/root/projects/hyperframes` (criada em 2026-07-12), ao lado do projeto Remotion
- 20 skills de agente instaladas nessa pasta (conteúdo real em `.agents/skills/`)
- Skills expostas globalmente via links simbólicos em `/root/.claude/skills` (28 links no total: 20 HyperFrames + 8 Remotion) — como o Giovani sempre roda o Claude no `/root`, as skills ficam disponíveis em toda sessão; as pastas `.claude` dentro dos projetos foram removidas por redundância

## Erros conhecidos

- `hyperframes browser ensure` falha com "no zip archiver is available" se o sistema não tiver
  `unzip` — resolvido instalando `unzip` via apt

## Status de validação

- Instalada e "Ready to render" ✓ — **nenhum vídeo real renderizado ainda**
- Skills instaladas e visíveis em sessão Claude no `/root` ✓ (verificado em 2026-07-12: as 28 apareceram na sessão ativa)

## Conexões

- [[wiki/projects/automacao-remotion.md|Automação Remotion]] — projeto que motivou a instalação
- [[wiki/tools/remotion.md|Remotion]] — ferramenta complementar para vídeos elaborados
