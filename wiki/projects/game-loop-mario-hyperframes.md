---
type: concept
tags: [projects, hyperframes, videos, parallax, pixel-art, mario, sprites]
title: Projeto game-loop — cenário de jogo com parallax (Mario) no HyperFrames
description: Vídeo de 10s simulando um cenário de jogo com parallax; evoluiu de recriação vetorial (SMB3) para sprites reais de Super Mario World (Yoshi's Island 2) com Mario correndo; inclui técnicas de recorte de sprite
timestamp: 2026-07-13T22:20:00-03:00
status: stable
---

# Projeto `game-loop` — cenário de jogo com parallax no HyperFrames

Em 2026-07-13 foi construído um vídeo de **10s (16:9)** simulando um cenário de jogo lateral
com **parallax** (camadas de fundo andando em velocidades diferentes), feito com
[[wiki/tools/hyperframes.md|HyperFrames]]. O projeto teve duas versões; a **v2 é a final**.

## Objetivo

Simular um cenário de jogo em que o fundo (nuvens/morros) se move numa velocidade e uma camada
mais próxima (chão) se move em outra — efeito **parallax scrolling** clássico do Mario.

## v1 — `/root/projects/hyperframes/game-loop` (recriação vetorial)

- Cenário do **Super Mario Bros 3** recriado em **SVG/CSS** (formas desenhadas), 4 camadas:
  nuvens (mais lento) → morros → arbustos → chão/canos/blocos "?" (mais rápido).
- Rejeitado pelo Giovani por parecer "moderno/vetorial" demais — ele queria **pixel-art 8-bit real**.

## v2 — `/root/projects/hyperframes/game-loop-v2` (sprites reais, **versão final**)

- Trocou para **Super Mario World (SNES, 16-bit)**, fase **Yoshi's Island 2**, com **sprites
  originais** que o próprio Giovani baixou e colocou em `assets/` (evita reproduzir arte
  proprietária por conta do agente; assets são do usuário, uso pessoal).
- **3 camadas de parallax:** fundo de morros verdes + nuvens (lento) → fase completa da
  Yoshi's Island 2 (rápido) → **Mario correndo** (5 frames de animação, ~12 fps).
- Ampliação com **pixels quadrados nítidos** (`image-rendering: pixelated`), sem borrão.
- Render final: 10s, 16:9, ~2.5 MB, ~1min de render na VPS.

## Técnicas de processamento de sprite (reutilizáveis)

Sprite sheets rippadas do SNES usam **cor-chave** (fundo teal chapado) em vez de transparência
real. Pipeline usado (via Pillow, instalado por apt junto com ImageMagick — não havia pip):

1. **Color-key:** identificar a família de cores teal do fundo e torná-la transparente.
2. **Flood fill a partir das bordas** (não por fórmula de cor global): remove só o fundo
   conectado à borda, **nunca fura o interior** do sprite (evitou o bug da "barriga transparente").
3. **Maior componente conectado:** manter só o maior bloco de pixels do sprite e descartar
   fragmentos soltos (ex.: pixel de rótulo "Walk"/"Run" que esticava a caixa e **cortava o topo**
   do Mario ao normalizar).
4. **Normalização:** cada frame num canvas de tamanho igual, **alinhado pelos pés** (bottom-center),
   pra o Mario não "tremer" ao trocar de frame.

## Técnicas de composição (reutilizáveis)

- **Tile de fundo espelhado sem emenda:** o recorte do fundo não é auto-repetível (borda esquerda
  = céu, direita = montanha). Solução: `tile = recorte + espelho_horizontal` (descartando a
  **coluna duplicada do meio**), o que faz céu encontrar céu e montanha encontrar montanha na
  repetição → parallax sem linha de emenda.
- **Esconder o vão do fundo:** a base das montanhas não desce até o chão; empurrar a camada de
  fundo pra baixo (atrás do chão opaco da frente) some com a faixa de céu que vazava.
- **Ciclo de corrida seek-safe:** animar uma **tira de sprites** com `ease: "steps(5)"` no GSAP
  (compatível com o render seek-safe do HyperFrames — ver [[wiki/tools/hyperframes.md|HyperFrames]]).
  Para não **piscar** no instante final do ciclo (célula vazia), a tira leva um **frame extra**
  (cópia do 1º) ao fim. Espelhar (`scaleX(-1)`) pra virar o Mario no sentido do movimento.

## Aprendizados de processo

- Fluxo iterativo com o Giovani: mostrar **snapshots** antes de renderizar (ele valida frame a
  frame), corrigir na raiz e só então gerar o MP4.
- `hyperframes init <nome>` cria a pasta-projeto sozinho — não precisa criar a pasta antes.

## Conexões

- [[wiki/tools/hyperframes.md|HyperFrames]] — ferramenta usada
- [[wiki/history/2026-07-13-primeiro-render-hyperframes.md|Primeiro render HyperFrames]] — render anterior (projeto estilos-animacoes)
