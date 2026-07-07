---
type: concept
tags: [projects, trello, n8n, evolution-api, whatsapp, automacao]
title: Automação Trello — Open Mídia
description: Automações sobre o board "DEMANDAS GERAIS | Open Mídia Digital" — primeiro fluxo (n8n) avisa no WhatsApp via Evolution quando um membro é adicionado a um card; validado em 2026-07-06
timestamp: 2026-07-07T10:05:00-03:00
status: draft
---

# Automação Trello — Open Mídia

Projeto de automações sobre o Trello da Open Mídia, começando pelo board **"DEMANDAS GERAIS | Open Mídia Digital"** (`6908bffbc7473c1134fe279d`, https://trello.com/b/GGYc7L1M, workspace "Criação - Clientes"). Construído em 2026-07-06 com o Giovani.

## Contexto do board

- ~233 cards (198 abertos, 35 arquivados em 2026-07-06); 8 listas, uma por cliente/frente
- **4 membros** (IDs reais do Trello, base pra qualquer roteamento):

| Membro | ID | Username |
|---|---|---|
| Giovani Gomes de Amorim | `69e148a7bc59b79cf77d3dcc` | giovanigomesdeamorim |
| Gabriele Lemos | `5fca48c6b1e8557a10d4da11` | gabrielelemos1 |
| Luciana Lemos | `55612c7040fe932af37bec26` | lemosluciana |
| Nathalia Bernardes | `5f46e3482f13ae46ee5f0d96` | nathaliabernardess |

## Fluxo 1 — Aviso de membro adicionado a card (funcionando)

**Workflow n8n:** `Teste-Trello-Membro-Adicionado` (ID `SiVxXjp2euu74SRO`), ativo e validado em 2026-07-06 (2 execuções reais de teste: auto-adição do Giovani e adição da Luciana pelo Giovani).

```
Trello Trigger (webhook do board) → Filter (só addMemberToCard) → HTTP "Buscar card no Trello" → Switch (por ID do membro)
  ├─ Giovani  → Evolution "Enviar texto - Giovani"
  ├─ Gabriele → Evolution "Enviar texto - Gabriele"
  ├─ Luciana  → Evolution "Enviar texto - Luciana"
  └─ Nathalia → Evolution "Enviar texto - Nathalia"
```

- **Gatilho:** nó `Trello Trigger` com o ID do board — ao ativar o workflow, o n8n registra webhook no Trello (push em tempo real; exige o n8n exposto publicamente, ok no nosso setup). Dispara pra **todo** evento do board; o Filter descarta o que não é `addMemberToCard`.
- **Buscar card no Trello** (adicionado 2026-07-07): nó HTTP Request `GET https://api.trello.com/1/cards/{{ $json.action.data.card.id }}?fields=name,due,shortUrl&list=true&list_fields=name`, autenticação por credencial predefinida `trelloApi` ("Trello account"). Necessário porque o payload do webhook `addMemberToCard` **não traz** vencimento nem lista do card. Como esse nó troca o `$json`, o Switch e as mensagens passam a referenciar os dados do gatilho via `$('Trello Trigger (DEMANDAS GERAIS)')`.
- **Mensagem** (expressão nos nós Evolution, formato definido pelo Giovani em 2026-07-07):

```
Oi, <Nome fixo do nó>, você foi adicionado ao card {{ $json.name }}

👤 Por {{ $('Trello Trigger (DEMANDAS GERAIS)').item.json.action.memberCreator.fullName.split(' ')[0] }}
➡️ {{ $json.list.name }}
🗓 Prazo: {{ $json.due ? new Date($json.due).toLocaleDateString('pt-BR', { timeZone: 'America/Sao_Paulo' }) : 'sem prazo definido' }}
🔗 Card no Trello: {{ $json.shortUrl }}
```
- **Envio:** nó comunitário da [[wiki/systems/evolution-api.md|Evolution API]], instância `Giobot`, credencial "Evolution account" (cadastrada na UI do n8n).

### Macetes do payload do webhook (custaram a descobrir)

- `action.member` / `action.data.idMember` = quem **foi adicionado** (usado no Switch)
- `action.memberCreator` = quem **executou a ação** (usado no "por Fulano")
- Nome do card: `action.data.card.name`
- Evento de membro adicionado ao **quadro** (não a card) é outro type: `addMemberToBoard`

### Pendências

- [ ] Trocar o `remoteJid` dos nós Gabriele/Luciana/Nathalia — os 3 ainda apontam pro número do Giovani (clones de teste)
- [ ] Renomear o workflow quando sair de teste

## Ideias futuras (desenhadas, não construídas)

- **Backup do board em Markdown + git:** exportar JSON do board e explodir em um arquivo por card (frontmatter, descrição, checklists, comentários, wikilinks pra lista/labels/membros), repo git próprio (ex.: `/root/trello-backup/`, fora da wiki) — git diff vira auditoria do que as automações mudaram. Regra anti-estrago: automações nunca usam Delete (só Archive, que é reversível).
- Alarme de eventos destrutivos (`deleteCard` etc.) via mesmo padrão Trigger→Filter.

## Referências

- Tabelas completas de ações das 3 integrações Trello (fora da wiki): `/root/mcp/n8n-trello-node.md`, `/root/mcp/trello-mcp-comunidade-acoes.md`, `/root/mcp/trello-mcp-oficial-acoes.md`

## Conexões

- [[wiki/systems/n8n.md|n8n]] — onde o workflow roda (ver seção Operação: criação de workflows via API REST)
- [[wiki/systems/evolution-api.md|Evolution API]] — envio de WhatsApp
- [[wiki/tools/trello-mcp.md|Trello MCP (comunidade)]] e [[wiki/tools/trello-mcp-oficial.md|Trello MCP (oficial)]] — acesso ao Trello pelos agentes
