---
type: concept
tags: [projects, trello, n8n, evolution-api, whatsapp, automacao]
title: Automação Trello — Open Mídia
description: Automações sobre o board "DEMANDAS GERAIS | Open Mídia Digital" — Fluxo 1 (n8n) avisa no WhatsApp quando um membro é adicionado a um card; Fluxo 2 manda lista semanal de prazos por membro. Ambos validados.
timestamp: 2026-07-07T23:50:00-03:00
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
- **Mensagem** (expressão nos nós Evolution, formato definido pelo Giovani em 2026-07-07). A saudação é **personalizada por nó** — Giovani ("adicionado"), Gabi, Lu e Nathalia ("adicionada") — e não deve ser reescrita em edições futuras:

```
Oi, <Saudação personalizada do nó>, você foi adicionad<o/a> ao card {{ $json.name }} por {{ $('Trello Trigger (DEMANDAS GERAIS)').item.json.action.memberCreator.fullName.split(' ')[0] }}

➡️ {{ $json.list.name }}
🗓 Prazo: {{ $json.due ? new Date($json.due).toLocaleDateString('pt-BR', { timeZone: 'America/Sao_Paulo' }) : 'sem prazo definido' }}
🔗 Card no Trello: {{ $json.shortUrl }}
```

> ⚠️ Incidente 2026-07-06/07: as saudações personalizadas do Giovani foram sobrescritas duas vezes por updates via API (PUT substitui o workflow inteiro). Recuperadas do snapshot de workflow guardado na execução 602. Regra: em qualquer edição via API, alterar **somente** o que foi pedido e preservar o resto verbatim.
- **Envio:** nó comunitário da [[wiki/systems/evolution-api.md|Evolution API]], instância `Giobot`, credencial "Evolution account" (cadastrada na UI do n8n).

### Macetes do payload do webhook (custaram a descobrir)

- `action.member` / `action.data.idMember` = quem **foi adicionado** (usado no Switch)
- `action.memberCreator` = quem **executou a ação** (usado no "por Fulano")
- Nome do card: `action.data.card.name`
- Evento de membro adicionado ao **quadro** (não a card) é outro type: `addMemberToBoard`

### Pendências

- [ ] Trocar o `remoteJid` dos nós Gabriele/Luciana/Nathalia — os 3 ainda apontam pro número do Giovani (clones de teste)
- [ ] Renomear o workflow quando sair de teste

## Fluxo 2 — Lista semanal de prazos por membro (funcionando, validado 2026-07-07)

**Workflow n8n:** `Trello Prazos por Membro - Open Mídia` (ID `PRaGCXrFKcmiusfE`), criado via API REST em 2026-07-07, testado com sucesso pelo Manual Trigger (200 cards processados, sem duplicação). Ainda **desativado** — falta ligar o Schedule Trigger em produção.

```
Schedule Trigger (segunda 8h) ─┐
Manual Trigger (teste)      ───┼→ Buscar cards do board (HTTP, 200 items)
                                └→ Buscar listas do board (HTTP, 9 items — sem saída conectada, só para o Code node referenciar por nome)
Buscar cards do board → Code "Filtrar e agrupar por membro" → Switch (por membro)
  ├─ Giovani  → Evolution "Enviar texto - Giovani"
  ├─ Gabriele → Evolution "Enviar texto - Gabriele"
  ├─ Luciana  → Evolution "Enviar texto - Luciana"
  └─ Nathalia → Evolution "Enviar texto - Nathalia"
```

- **Gatilhos:** `Schedule Trigger` semanal (segunda-feira 8h) + `Manual Trigger` de teste, ambos ligados em paralelo direto nos dois nós HTTP (não um atrás do outro — ver bug abaixo). Pendência: remover o Manual quando o fluxo for pra produção.
- **Buscar cards do board:** `GET /1/boards/{id}/cards?fields=name,due,shortUrl,idList,idMembers`.
- **Buscar listas do board:** `GET /1/boards/{id}/lists?fields=name` — sem conexão de saída no canvas; o Code node lê os dados por referência (`$('Buscar listas do board').all()`), não por fio. Isso é válido no n8n: basta o nó ter executado na mesma run, a conexão direta não é exigida.
- **Code "Filtrar e agrupar por membro"** (JS, `runOnceForAllItems`):
  - Janela rolante: hoje até hoje + 7 dias, calculada explicitamente na timezone `America/Sao_Paulo` (ver bug de timezone abaixo)
  - Descarta cards sem `due` ou fora da janela
  - Limpa nome da lista: remove prefixo `^\d+\s*-\s*` e corta no `|` (ex.: `"0 - OPEN MÍDIA | 4 CONTEÚDOS POR SEMANA"` → `"OPEN MÍDIA"`)
  - Exceção fixa por ID de lista: `6984b4d0a73553b1402d0838` ("1 - LIBERTAS ASSISTENTE VIRTUAL | ...") → `"LIBERTAS"` (não dá pra pegar por regex)
  - Agrupa por membro (`idMembers`; card com múltiplos membros aparece pra cada um) e, dentro do membro, por lista (ordem = posição da lista no board) e por prazo (mais próximo primeiro)
  - Monta a saudação + mensagem única por membro em `$json.message`
- **Switch:** mesmos 4 IDs de membro do Fluxo 1, testando `$json.memberId`.
- **Envio:** mesmo padrão do Fluxo 1 (Evolution, instância `Giobot`, credencial "Evolution account"), `remoteJid` dos 4 nós ainda apontando pro número do Giovani — mesma pendência do Fluxo 1, resolve junto.

**Formato da mensagem** (validado com o Giovani em 2026-07-07, com apelidos do padrão do Fluxo 1 — Giovani, Gabi, Lu, Nathalia):

```
Oi, Lu!

📋 Aqui estão suas tarefas próximas (de 07/07 a 14/07):

*LIBERTAS*

📌 Estático | Planilha não é gestão financeira
🗓️ Vence em *09/07/2026*
🔗 https://trello.com/c/k0zxpTRg
```

### Dois bugs achados e corrigidos durante o teste

1. **Cards duplicados 9x:** a primeira versão encadeava `Buscar listas do board` (9 itens, um por lista) → `Buscar cards do board`. O nó HTTP Request do n8n roda **uma vez por item de entrada** por padrão — com 9 itens chegando, ele disparou a busca de cards 9 vezes, duplicando cada card 9x na saída. Correção: os dois HTTP passaram a rodar em paralelo direto dos gatilhos, sem um alimentar o outro (ver diagrama acima).
2. **Switch com 2 saídas mortas:** ao reler o workflow após a primeira edição via API, as saídas do Switch para Gabriele e Nathalia estavam sem nó conectado (`[]`) — só Giovani e Luciana recebiam mensagem. Causa não totalmente clara (possível efeito colateral de PUT parcial via API); corrigido reconstruindo as 4 conexões do Switch explicitamente. Vale checar esse padrão em qualquer Switch editado via API daqui pra frente.

### Pendências

- [ ] Remover o Manual Trigger depois que o fluxo rodar em produção
- [ ] Ativar (`active: true`) quando o Giovani decidir ligar o Schedule
- [ ] Resolver junto com a pendência do Fluxo 1: `remoteJid` dos 4 nós Evolution

## Ideias futuras (desenhadas, não construídas)

- **Backup do board em Markdown + git:** exportar JSON do board e explodir em um arquivo por card (frontmatter, descrição, checklists, comentários, wikilinks pra lista/labels/membros), repo git próprio (ex.: `/root/trello-backup/`, fora da wiki) — git diff vira auditoria do que as automações mudaram. Regra anti-estrago: automações nunca usam Delete (só Archive, que é reversível).
- Alarme de eventos destrutivos (`deleteCard` etc.) via mesmo padrão Trigger→Filter.

## Referências

- Tabelas completas de ações das 3 integrações Trello (fora da wiki): `/root/mcp/n8n-trello-node.md`, `/root/mcp/trello-mcp-comunidade-acoes.md`, `/root/mcp/trello-mcp-oficial-acoes.md`

## Conexões

- [[wiki/systems/n8n.md|n8n]] — onde o workflow roda (ver seção Operação: criação de workflows via API REST)
- [[wiki/systems/evolution-api.md|Evolution API]] — envio de WhatsApp
- [[wiki/tools/trello-mcp.md|Trello MCP (comunidade)]] e [[wiki/tools/trello-mcp-oficial.md|Trello MCP (oficial)]] — acesso ao Trello pelos agentes
