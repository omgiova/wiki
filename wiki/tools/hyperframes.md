---
type: tool
tags: [tools, hyperframes, videos, html, heygen]
title: HyperFrames
description: CLI open-source da HeyGen que renderiza HTML/CSS/JS em vídeo MP4 — feita para agentes de IA; instalada globalmente na VPS, primeiro vídeo renderizado em 2026-07-13
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
- **Criar um projeto novo:** `hyperframes init <nome>` cria a pasta `<nome>` já com o esqueleto do vídeo dentro — **não precisa criar a pasta antes**. A pasta nasce **onde você estiver** no momento, então rodar sempre a partir de `/root/projects/hyperframes` (senão a pasta cai no lugar errado, ex. `/root/<nome>`). Dá pra já definir o formato: `--resolution portrait` (9:16 vertical) ou `--resolution landscape` (16:9, padrão). Obs: `init` **não instala** a ferramenta (já é global na VPS) — só cria a pasta-projeto.
- Projetos de vídeo centralizados em `/root/projects/hyperframes`
- Skills disponíveis em qualquer sessão Claude iniciada no `/root` (ver Configuração)
- Ainda sem workflow definido — aguardando definição do projeto [[wiki/projects/automacao-videos.md|Automação Vídeos]]

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
- **Replay/câmera lenta precisa ser seek-safe:** o render dá *seek* (pulo) para cada frame e lê
  o estado no instante — `.from()` empilhado no mesmo elemento **não** é seek-safe e renderiza
  errado. Usar `set()` + `fromTo()` com `immediateRender: false`. (Descoberto no
  [[wiki/history/2026-07-13-primeiro-render-hyperframes.md|primeiro render]].)

## Status de validação

- Instalada e "Ready to render" ✓
- Skills instaladas e visíveis em sessão Claude no `/root` ✓ (verificado em 2026-07-12: as 28 apareceram na sessão ativa)
- **Primeiro vídeo real renderizado em 2026-07-13** ✓ — projeto `estilos-animacoes` (72s, 9:16,
  12 estilos de animação motion, com nome técnico + parâmetros na tela). Ver
  [[wiki/history/2026-07-13-primeiro-render-hyperframes.md|history]].

## Conexões

- [[wiki/projects/automacao-videos.md|Automação Vídeos]] — projeto que motivou a instalação
- [[wiki/tools/remotion.md|Remotion]] — ferramenta complementar para vídeos elaborados
- [[wiki/history/2026-07-13-primeiro-render-hyperframes.md|Primeiro render]] — projeto estilos-animacoes (motion graphics)
- [[wiki/history/2026-07-13-game-loop-mario-hyperframes.md|Projeto game-loop (Mario)]] — parallax com sprites reais do SNES + técnicas de recorte de sprite (color-key, flood fill, tile espelhado)
