---
type: concept
tags: [projects, open-midia, iscas, posicionamento, marketing]
title: Isca — Diagnóstico de Presença Digital (Open Mídia)
description: Diagnóstico modular de presença digital como isca de captação da Open Mídia, estruturado sobre o modelo CBBE de Keller — 4 focos, 8 perguntas, 36 cruzamentos.
timestamp: 2026-07-29T22:39:00-03:00
status: draft
---

# Isca — Diagnóstico de Presença Digital (Open Mídia)

## O que é

Um diagnóstico gratuito de presença digital para marcas, negócios e profissionais, usado como isca de captação da Open Mídia: o usuário responde 8 perguntas de múltipla escolha (3 opções cada), divididas em 4 focos de 2 perguntas, e recebe de volta 4 frases de diagnóstico — uma por foco — em que cada frase é o resultado do cruzamento prévio das duas respostas daquele bloco, apontando o que já está construído, qual lacuna permanece aberta e qual é o próximo passo estruturante; o valor do diagnóstico está justamente em ter todos os cruzamentos escritos com antecedência, o que permite entregar uma leitura específica em vez de um resultado genérico, e nenhuma combinação — nem a melhor possível — se encerra sem apontar um avanço possível, porque o topo da escala reconhece a prática mas revela que ela ainda não é método documentado, auditável e replicável.

## Quantidades

| Item | Quantidade |
|---|---|
| Focos | 4 |
| Perguntas por foco | 2 |
| Total de perguntas | 8 |
| Opções por pergunta | 3 (A, B, C) |
| Cruzamentos por foco | 9 (matriz 3×3) |
| **Total de cruzamentos a redigir** | **36** |
| Frases entregues ao usuário | 4 |

Dos 9 cruzamentos de cada foco, 3 são de diagonal (as duas respostas no mesmo grau, cenário coerente) e 6 são de desequilíbrio — onde está o diagnóstico mais valioso, porque revelam que uma frente avançou sem a outra.

## Metodologia

O diagnóstico é construído sobre o modelo **Customer-Based Brand Equity (CBBE)** de **Kevin Lane Keller**, professor da Tuck School of Business (Dartmouth) e co-autor do *Marketing Management* com Philip Kotler. O modelo organiza a construção de marca em 4 níveis sequenciais, e sua regra central é que cada nível precisa estar consolidado antes do próximo — uma marca não adquire autoridade, ela a constrói de baixo para cima ao longo do tempo. Os 4 focos do diagnóstico correspondem exatamente aos 4 níveis do modelo, o que permite que o resultado aponte em qual nível a marca está travada.

Como referência de apoio nos textos das frases (não na estrutura): **Ehrenberg-Bass Institute** (disponibilidade mental e ativos distintivos), **Binet & Field / IPA** (proporção 60/40 entre construção de marca e ativação de vendas) e o **Relatório Edelman-LinkedIn de Liderança de Pensamento**, que fornece os dados para quantificar o custo da inação.

## Os 4 focos

| Foco | Nível CBBE | O que mede |
|---|---|---|
| 1 | **Saliência** | A marca é lembrada — presença regular e reconhecível |
| 2 | **Significado** | O que a marca representa — território, público, imagem |
| 3 | **Resposta** | Como o mercado julga a marca — credibilidade e diferenciação |
| 4 | **Ressonância** | O mercado procura, indica e defende a marca |

## Os 8 eixos

Cada foco tem 2 eixos complementares: um mede a prática interna, o outro mede o resultado percebido de fora.

| Foco | Eixo | O que mede |
|---|---|---|
| 1 | Presença regular | A marca aparece em ritmo previsível e sustentado |
| 1 | Reconhecimento imediato | A marca é identificável por elementos estáveis |
| 2 | Território e público | A marca comunica com precisão o que entrega e para quem |
| 2 | Imagem e narrativa | A marca transmite valores, critérios e forma de trabalhar |
| 3 | Credibilidade demonstrada | A marca sustenta o que afirma com análise, casos ou dados |
| 3 | Diferenciação percebida | O mercado sabe por que escolher esta marca e não outra |
| 4 | Vínculo e recorrência | Clientes e audiência retornam e permanecem |
| 4 | Procura espontânea | O mercado busca a marca sem estímulo pago |

## Escala de resposta

| | Estado | Lacuna que o diagnóstico levanta |
|---|---|---|
| **A** | Não praticado | Ausência do bloco |
| **B** | Praticado sem critério — varia conforme a demanda do período | Sem previsibilidade; o resultado não acumula |
| **C** | Praticado de forma consistente | Não documentado como método: não é auditável, delegável, nem sobrevive a aumento de escala |

Duas decisões de desenho: as opções descrevem apenas realidade observável, sem julgamento e sem condições empilhadas — todo o argumento sai do cruzamento, não do texto da opção; e a condição do nível C ("não está registrado em calendário editorial", "não está definido em padrão escrito") nunca aparece na opção, apenas na frase de diagnóstico.

## Protótipo

Rodando no repositório de UI da Open Mídia, [github.com/omgiova/om](https://github.com/omgiova/om), na rota **`/diagnostico`** (commits `0898909` e `f7240e6`).

| Item | Onde |
|---|---|
| Página | `om-site/app/diagnostico/page.tsx` |
| Conteúdo | `om-site/public/dados/diagnostico.csv` — 36 linhas, 16 colunas |
| Local | dev em `npm run dev` (porta 4319 nos testes) |

Decisões do protótipo:

- **Todo o conteúdo vem do CSV**, lido em tempo de execução: enunciados, subtítulos, textos das opções e as 36 frases. Editar o CSV muda a página sem mexer em código.
- **Visual deliberadamente sem marca** — Arial preto sobre branco, caixa de borda fina, sem cor, ícone ou sombra — para não interferir na avaliação do conteúdo pela Open Mídia. A sidebar do site fica oculta nessa rota.
- **Uma pergunta por vez**, com contador e botão de voltar. O resultado traz um parágrafo curto sobre a metodologia (sem citar nomes de níveis nem quantidades) seguido das 4 frases, sem título de nível.
- **Botão de engrenagem** abaixo da caixa abre "Ver respostas", que lista os 36 cruzamentos agrupados pelos 4 níveis — revisão de conteúdo sem responder o formulário.
- O CSV ficou em `public/dados/` e não em `public/diagnostico/` porque o build estático gera `diagnostico.html` no mesmo nível, e a colisão de caminho poderia fazer a Netlify servir a pasta em vez da página.

Score e timeline ainda não foram implementados — a coluna `pontos` do CSV (2 a 6 por foco, 8 a 24 no total) já está pronta para isso.

## Perguntas — Foco 1 (Saliência)

### 1. Com que previsibilidade sua marca publica?
*Se alguém abrir seus canais, encontra publicações em intervalos parecidos ou longos períodos sem nada?*

| | |
|---|---|
| **A** | Não há ritmo definido. A publicação acontece quando surge uma novidade ou quando sobra tempo. |
| **B** | A frequência oscila conforme a demanda de cada período. |
| **C** | A frequência se mantém de forma consistente. |

### 2. Sua marca é reconhecível de imediato?
*Alguém saberia que a publicação é sua sem ver o nome ou logo?*

| | |
|---|---|
| **A** | Provavelmente não. Cada publicação segue um estilo, formato ou tom diferente. |
| **B** | Em parte. Alguns elementos se repetem, outros mudam com frequência ou variam de um canal para outro. |
| **C** | Sim. Cores, tipografia, tom ou formatos se repetem de forma reconhecível. |

## Perguntas — Foco 2 (Significado)

### 3. Está claro o que sua marca faz e para quem?
*Quem chega pela primeira vez entende sozinho ou precisa perguntar?*

| | |
|---|---|
| **A** | Provavelmente precisa perguntar. A descrição serve para quase qualquer público. |
| **B** | Em parte. Uma das duas coisas está clara: a entrega ou o público. |
| **C** | Sim. A entrega e o público aparecem com precisão. |

### 4. Sua marca comunica como pensa, além do que vende?
*Seu conteúdo fala só da oferta ou também dos seus critérios e do seu jeito de trabalhar?*

| | |
|---|---|
| **A** | Fala principalmente da oferta: serviços, produtos, condições, resultados. |
| **B** | O jeito de pensar aparece de vez em quando, sem um fio condutor. |
| **C** | Os critérios e a forma de trabalhar aparecem com frequência. |

## Perguntas — Foco 3 (Resposta)

### 5. Sua marca comprova o que afirma?
*O que você diz vem acompanhado de análise própria, casos ou números?*

| | |
|---|---|
| **A** | Raramente. A comunicação se sustenta em afirmações e promessas. |
| **B** | Às vezes, quando há um caso ou resultado disponível para mostrar. |
| **C** | Com frequência. Análises, casos ou dados fazem parte da comunicação. |

### 6. O mercado sabe por que escolher sua marca?
*Se comparassem sua marca com um concorrente parecido, a diferença ficaria evidente?*

| | |
|---|---|
| **A** | Provavelmente não. A comparação tende a cair no preço ou no prazo. |
| **B** | Em parte. A diferença existe, mas depende de uma conversa para aparecer. |
| **C** | Sim. A diferença aparece antes do primeiro contato. |

## Perguntas — Foco 4 (Ressonância)

### 7. Seus clientes e sua audiência permanecem?
*Tem gente que volta, acompanha e continua por perto depois do primeiro contato?*

| | |
|---|---|
| **A** | Pouco. Cada venda ou contato começa e termina em si mesmo. |
| **B** | Alguns permanecem, sem um padrão claro. |
| **C** | Sim. Há um grupo que acompanha e retorna com regularidade. |

### 8. O mercado procura sua marca sem estímulo pago?
*Chega contato por indicação, convite ou busca — sem anúncio e sem prospecção?*

| | |
|---|---|
| **A** | Raramente. Quase todo contato vem de prospecção ativa ou anúncio. |
| **B** | Acontece de vez em quando, de forma imprevisível. |
| **C** | Sim, com regularidade. Indicação e busca são canais reais de entrada. |

## Estado de validação

| Item | Estado |
|---|---|
| 4 focos e correspondência com os níveis CBBE | validado pelo Giovani |
| 8 eixos | validados pelo Giovani |
| Escala de resposta A/B/C | validada pelo Giovani |
| Perguntas 1 e 2 (texto, subtítulo e opções) | validadas pelo Giovani, palavra por palavra |
| Perguntas 3 a 8 | escritas, ainda não revisadas uma por uma |
| 36 cruzamentos | escritos, ainda não revisados um por um |
| Protótipo de tela | aprovado pelo Giovani no navegador |

## Em aberto

- Revisão das perguntas 3 a 8 e dos 36 cruzamentos
- Score e timeline no resultado (gamificação) — a coluna `pontos` do CSV já sustenta
- Validação do conjunto pela Open Mídia
- Formato de captação e canal de aplicação (onde a isca fica hospedada, o que se pede em troca)

## Conexões

- [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]]
