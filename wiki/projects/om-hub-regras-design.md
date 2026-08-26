---
type: concept
tags: [om-hub, ui, regras-design, projeto]
title: "OM Hub — Regras de Design"
description: "Regras universais de UI/design do projeto OM Hub (timesheet + banco de referências da agência), decididas pelo Giovani."
timestamp: 2026-08-26T14:00:00-03:00
status: stable
---

# OM Hub — Regras de Design

Fonte: decisões diretas do Giovani durante o desenvolvimento (repo privado `omgiova/om-hub`, Next.js + Tailwind). Estas regras valem para TODA página e componente novo.

## Regras universais

1. **PROIBIDO EYEBROW.** Nenhum overline uppercase acima de título ("OPERAÇÃO · SEMANA CORRENTE" e similares). Nunca introduzir esse padrão em nenhuma página — nem em componentes compartilhados como PageHeader.
2. **Sem legendas que poluem KPIs** — ex.: "3 lançamentos de refação no recorte" sob o número-chefe. O número fala por si.
3. **Sem subtítulo descritivo redundante** sob títulos de página ("Tempo normal e refações da equipe inteira…"). Ações e dados explicam a página.

## Identidade visual aprovada (Versão B)

| Elemento | Decisão |
|---|---|
| Tema | Único — modo noturno quente (carvão com subtom amarelo-marrom `#141413`-ish) |
| Cor de marca | Lavanda pálida `#B4A7D6` (única cor de identidade; botão primário, underlines, avatares) |
| Serifa display | **Fraunces** (títulos + número-chefe) |
| Sans corporal | **Hanken Grotesk** (corpo denso, tabela) |
| Status | Tons editoriais empoeirados: andamento=lavanda · refação=rosa-argila `#D4A5A5` · revisão=ocre-areia `#D6BE8A` · concluída=sálvia `#A9C4B2` · pausada=cinza-quente `#8A8580`. **Nada de verde/vermelho clichê.** |
| Anatomia | Editorial: masthead horizontal (sem sidebar), número gigante serifado à esquerda + ledger com hairlines à direita, tabs underline, tabela solta sem card wrapper |
| Composição assimétrica > cards iguais | 4 KPI cards idênticos = anti-pattern rejeitado |

## Rejeitados pelo usuário

- Coral/terracota como accent (`#c96442`/`#d97757`) — dominava demais em dark
- Verde+vermelho semântico nos status — clichê
- Versão A dark-precision estilo Linear com sidebar vertical — descartada junto com seu scaffold
- Eyebrows e legendas descritivas — poluem

## Mecânica de domínio (agência)

- Uma tarefa pode ser executada por **1..n pessoas**, simultâneas ou não (`Task.assigneeIds`)
- Cada lançamento de tempo acontece dentro de uma **etapa**: Planejamento, Redação, Design, Desenvolvimento, Revisão interna, Publicação, Outro (`TimeEntry.stage`)
- Lançamento distingue normal vs refação (`TimeEntry.kind`) — fonte da verdade de todos os agregados
- Colaborador vê só onde participa; admin vê tudo

## Conexões

- [[wiki/projects/index.md]]
