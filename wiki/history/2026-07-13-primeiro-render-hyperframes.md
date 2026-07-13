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
  **6 estilos de motion diferentes**: 1) fade + escala, 2) deslizar, 3) girar, 4) quicar (bounce),
  5) pop em sequência, 6) zoom.
- **Formato:** vertical 9:16 (1080×1920), gerado via skill `/motion-graphics`, animação GSAP.
- **Duração final:** 36s — cada cena tem 6s (2s em velocidade normal + 4s repetindo a mesma
  animação em câmera lenta, a meia velocidade). Pedido do Giovani para ver as animações em detalhe.
- **Render:** `hyperframes render` na VPS (2 vCPU, low-memory profile, 1 worker); ~1m35s para os 36s.

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
