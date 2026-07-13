---
type: session
tags: [history, hyperframes, videos, render, motion-graphics]
title: Primeiro render real com HyperFrames — projeto estilos-animacoes
description: Primeiro vídeo de fato renderizado com HyperFrames na VPS (36s, 9:16, 6 estilos de animação), incluindo a descoberta do requisito seek-safe
timestamp: 2026-07-13T00:10:00-03:00
status: stable
---

# Primeiro render real com HyperFrames — projeto `estilos-animacoes`

Em 2026-07-13 foi renderizado o **primeiro vídeo de fato** com [[wiki/tools/hyperframes.md|HyperFrames]]
na VPS, validando toda a instalação feita no dia anterior. Motivado pelo projeto
[[wiki/projects/automacao-videos.md|Automação Vídeos]].

## O que foi feito

- **Pasta:** `/root/projects/hyperframes/estilos-animacoes` (inicialmente `projeto1`, renomeada após aprovação)
- **Vídeo:** "Hello World" acompanhado de um quadrado, um círculo e um triângulo, animados em
  **12 estilos de motion diferentes** (cobrindo o vocabulário de entrada/ênfase da skill):
  1) `scale_grow`, 2) `slide` multi-direção, 3) `spin`/rotate, 4) `bounce_in`, 5) `scale_punch` (pop),
  6) `zoom`, 7) `fade_blur`, 8) `slam`+`shake`, 9) `wave`, 10) `typewriter` (clipPath), 11) `glow`
  (drop-shadow yoyo), 12) `shake` (keyframes).
- **Formato:** vertical 9:16 (1080×1920), gerado via skill `/motion-graphics`, animação GSAP.
- **Duração final:** 72s — 12 cenas de 6s cada (2s em velocidade normal + 4s repetindo a mesma
  animação em câmera lenta, a meia velocidade, via multiplicador `k`). Pedido do Giovani para ver
  as animações em detalhe.
- **Legendas didáticas:** cada cena mostra na tela o **nome técnico real** (primitivo do vocabulário
  + ease GSAP) e os **parâmetros usados** (from, duração, stagger, ease) — valores da velocidade normal.
- **Render:** `hyperframes render` na VPS (2 vCPU, low-memory profile, 1 worker); ~4m13s para os 72s.
- **Escopo:** este é o **tipo 1** (motion graphic de formas+texto) dos 10 tipos que a skill cobre;
  plano combinado é fazer um vídeo por tipo, e depois um vídeo dedicado só a transições.

## Descoberta técnica — render é seek-safe

O HyperFrames **não "toca" a timeline**: ele renderiza dando *seek* (pulo) para cada frame e lê o
estado do elemento naquele instante. Consequência prática descoberta na prática:

- `.from()` **empilhado no mesmo elemento** (usado numa 1ª tentativa para fazer o replay lento)
  **não é seek-safe** — `immediateRender` captura valores no build e o estado fica ambíguo ao dar
  seek, então o trecho lento renderiza errado.
- **Correção:** `set()` (estado inicial) + `fromTo()` com `immediateRender: false`, que torna o
  estado determinístico em qualquer frame. Sintoma visível da correção: o MP4 saltou de ~0,6 MB
  para ~2,3 MB (o trecho lento passou a ter movimento real em vez de frames parados idênticos).

## Aprendizados de processo

- Interpretação inicial errada: "6 estilos de animação" foi lido como efeitos de texto; o Giovani
  queria **formas geométricas + texto** animando juntos. Corrigido na 2ª iteração.
- Iteração rápida: 3 renders até a versão aprovada (base 12s → 36s com câmera lenta → correção seek-safe).

## Conexões

- [[wiki/tools/hyperframes.md|HyperFrames]] — ferramenta usada (Erros conhecidos atualizado com o requisito seek-safe)
- [[wiki/projects/automacao-videos.md|Automação Vídeos]] — projeto que motivou o teste
