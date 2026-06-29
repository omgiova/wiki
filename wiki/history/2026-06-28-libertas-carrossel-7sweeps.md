---
type: session
tags: [libertas, copywriting, revisao, 7-sweeps, carrossel]
title: Revisão Libertas Carrossel — 7 Sweeps (conversion-copywriting)
description: Aplicação dos 7 Sweeps do conversion-copywriting no carrossel "Financeiro estratégico não é cortar custo. É poder investir" da Libertas, com insumos do guia de voz raw/libertas.md.
timestamp: 2026-06-28T15:00:00-03:00
status: stable
---

# Revisão Libertas Carrossel — 7 Sweeps

## Contexto

Durante sessão no Hermes (28/jun/2026), o usuário solicitou revisão de qualidade do conteúdo do carrossel **"Financeiro estratégico não é cortar custo. É poder investir"** da Libertas Assessoria Financeira.

## Arquivos envolvidos

| Arquivo | Função |
|---------|--------|
| `/root/Libertas/carrossel-financeiro-investimento.md` | v1 original |
| `/root/Libertas/carrossel-financeiro-investimento-v2.md` | v2 — base da revisão |
| `/root/Libertas/carrossel-financeiro-investimento-v3.md` | v3 — cópia do v2 (criada como recipiente para alterações) |
| `[[raw/libertas.md]]` | Guia de voz da Libertas no raw/ da wiki |
| `/root/Libertas/` | Pasta de trabalho externa à wiki (não versionada na wiki) |

## Skills utilizadas

- **`conversion-copywriting`** — usada em **duas etapas**:
  1. **Criação (v1):** skill escolhida aleatoriamente pelo usuário ("escolha uma skill aleatória de copywriting"). Metodologia Joanna Wiebe aplicada: leitura do guia `raw/libertas.md`, extração de VOC, estrutura PAS (Problem-Agitate-Solve), messaging hierarchy, draft com split-screen method. Entregou carrossel de 7 cards + legenda.
  2. **Revisão (v2→v3):** framework **7 Sweeps Editing Process** (Clarity, Voice, Proof, Specificity, Stickiness, Emotion, Zero) aplicado contra briefing + guia de voz.
- **`social`** (referências) — `references/platform-limits.md` e `references/post-templates.md` usados como contexto complementar na revisão

## Metodologia

Os 7 Sweeps foram aplicados comparando o conteúdo do carrossel (v2/v3) contra dois insumos:

1. **Briefing do próprio arquivo:** "Quebrar a associação financeiro = aperto. Mostrar finanças como motor de investimento em estrutura e mão de obra, não de corte."
2. **Guia de voz Libertas** (`raw/libertas.md`): adjetivos de tom (Próxima, Acessível, Descomplicada, Estratégica, Humanizada), regras de linguagem, recursos que funcionam, o que evitar, exemplos de copies aprovadas.

## Findings (10 intervenções)

| # | Sweep | Elemento | Problema | Sugestão |
|---|-------|----------|----------|----------|
| 1 | Zero (7) | TELA 2 | "cortar gastos" + "enxugar o orçamento" repetem mesmo conceito; "enfim" é filler | Unificar e cortar filler |
| 2 | Zero (7) | TELA 4 | "parece que precisa" repete duas vezes na mesma tela, perdendo impacto | Substituir primeira ocorrência por "decidir no achismo" |
| 3 | Specificity (4) | TELA 3 bullets | Exemplos genéricos ("contratar", "sistema", "expandir", "marketing") sem contexto concreto | Adicionar especificidade: "contratar aquele analista", "trocar de ERP", "expandir sem susto no caixa" |
| 4 | Emotion (6) | Legend CTA | "Quer ver como isso funciona na prática?" — fecha no funcional, não na sensação | Substituir por "Quer sentir o que é tomar decisão sem aquele frio na barriga?" |
| 5 | Voice (2) | Carrossel geral | Ausência de comparações do cotidiano (regra explícita do guia: "usa comparações do cotidiano") | Adicionar analogia cotidiana na TELA 2 ("como pagar conta com o olho no relógio") |
| 6 | Voice (2) | TELA 5 | "fluxo de caixa projetado" é termo técnico sem explicação prévia (viola regra "termos técnicos → explique com comparação") | Explicar antes de nomear: "Com a visão do que vai entrar e sair nos próximos meses — fluxo de caixa projetado" |
| 7 | Stickiness (5) | Arco narrativo | "será que posso?" funciona bem mas é o único gancho recorrente; falta contraponto de certeza | Criar arco "será que posso?" → "eu sei quando e quanto" |
| 8 | Emotion (6) | Carrossel geral | Ausência de "tranquilidade" como resultado emocional (guia define 3 sensações: liberdade, clareza, tranquilidade) | Inserir "tranquilidade" na TELA 5 ("você dorme sabendo que o mês seguinte está resolvido") |
| 9 | Zero (7) | Legend parágrafo 2 | "Corte de gastos, redução" — repetição do mesmo conceito; "essa associação está errada" é formulação formal | Unificar e tornar mais coloquial ("Só que não é bem assim") |
| 10 | Clarity (1) | Capa subheadline | "cada recurso" é vago; leitor precisa decodificar se é dinheiro, tempo ou pessoas | Substituir por "cada real" — concreto e sem ambiguidade |

## Decisões

- **Nada foi aplicado ao v3** por decisão do usuário. As intervenções ficam como documentação para referência futura.
- O v3 permanece como cópia idêntica do v2, pronto para receber alterações quando o usuário autorizar.
- A skill `conversion-copywriting` mostrou-se adequada para revisão de conteúdo de social media mesmo não sendo específica para o formato — os 7 Sweeps são genéricos o suficiente para copy de carrossel.

## Conexões

- [[raw/libertas.md|Guia de Voz — Libertas]]
- [[wiki/concepts/okf.md|Open Knowledge Format (OKF)]]
- `conversion-copywriting` skill (skill_view)
