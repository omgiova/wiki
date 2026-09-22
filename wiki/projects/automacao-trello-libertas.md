---
type: concept
tags: [projects, trello, n8n, evolution-api, whatsapp, automacao, libertas]
title: Automação Trello — Libertas
description: Fluxo único n8n sobre o board "Libertas - Assist Virtual Financeiro" — avisa Thairine e Luciana no WhatsApp sobre 4 tipos de evento do Trello, com janela 9h-18h e fila fora do horário; construído e validado em 2026-09-22.
timestamp: 2026-09-22T20:35:00-03:00
status: draft
---

# Automação Trello — Libertas

> ⚠️ **Vai escrever no fluxo via API (editar/criar/Data Table/ativar)?** Ler antes [[wiki/systems/n8n.md|n8n]] §Criar/editar workflows via API REST. PUT substitui o workflow inteiro: alterar **somente** o pedido e devolver o resto verbatim.

Automação sobre o board **"Libertas - Assist Virtual Financeiro"** (`674a36661a4746aa68d2e485`, https://trello.com/b/IgWlIebX). Diferente da [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]], que tem 6 fluxos separados, aqui tudo vive em **um fluxo só**. Construído com o Giovani em 2026-09-22.

## Contexto do board

- **3 membros** (IDs reais do Trello):

| Membro | ID | Username |
|---|---|---|
| Giovani Gomes de Amorim | `69e148a7bc59b79cf77d3dcc` | giovanigomesdeamorim |
| Luciana Lemos | `55612c7040fe932af37bec26` | lemosluciana |
| Thairine Santana | `645a867e0534c96eb6241745` | thairinesantana1 |

- **27 listas abertas** (de 39 no total) em 2026-09-22. Organização **por semana**, não por cliente: listas do tipo `SETEMBRO | SEMANA 1`, `AGOSTO SEMANA 4`, `MARÇO |  SEMANA 03` (as três grafias convivem), mais listas fixas (`Infos importantes`, `Banco de Temas`, `Edição de vídeo`, `ARQUIVADO`, `2025`) e de canal (`BLOG`, `LINKEDIN`). **Listas novas nascem toda semana** — por isso o fluxo traduz o nome da lista por regex, e não por um mapa de IDs como o Fluxo 1 da Open Mídia (mapa fixo ficaria obsoleto em dias, falhando em silêncio).
- **Etiqueta `APROVADO`**: `674a3667021bc52454ce03d8` (verde). Existe também `APROVADO COM ALTERAÇÃO` (`674a366732f6732d5f14a40a`) — por isso o filtro é por **ID exato**, nunca por nome contendo "APROVADO".

## O fluxo

**Workflow n8n:** `Trello Libertas - Fluxo único` (ID `xOaO43iqm1bz0b8M`), 18 nós, ativo e validado em 2026-09-22. Error Workflow "Alerta de Erro" (`3MI1k15YL5OUrEXF`) apontado desde a criação.

```
Trello Trigger (board Libertas) → Filter "Eventos do fluxo" (addMemberToCard | commentCard | addLabelToCard)
  → HTTP "Buscar card no Trello" → Code "Classificar destinatárias" → Code "Horário comercial?" → If "Fora do horário?"
      ├─ fora  → Data Table "Guardar na fila"
      └─ dentro → Switch "Quem recebe?" → Evolution "Enviar texto - Thairine" / "- Luciana"

Schedule 9h (seg-sex) → "Ler fila" → HTTP "Buscar card da fila" → Code "Montar mensagem agrupada"
  → Switch "Quem recebe? (fila)" → Evolution "Fila - Thairine" / "Fila - Luciana" → "Limpar fila"
```

### Quem recebe o quê

| Evento | Trello | Thairine | Luciana |
|---|---|---|---|
| Adicionada a um card | `addMemberToCard` | 🔵 | — |
| Menção em comentário | `commentCard` | 🟡 | 🟡 |
| Resposta em comentário | `commentCard` | 🟡 | 🟡 |
| Etiqueta APROVADO | `addLabelToCard` | — | 🟢 |

### Horário

Seg a sexta, **9h às 18h** (`minutos >= 540 && minutos < 1080`, timezone `America/Sao_Paulo`). Fora disso o evento vira linha na fila, enviada às **9h do próximo dia útil** (Schedule `0 9 * * 1-5`). Não colide com as filas da Open Mídia (10:10 e 10:11).

### Regra da "resposta" e a trava anti-duplicidade

O Trello **não tem resposta encadeada**: uma resposta chega como `commentCard` comum, sem referência ao comentário-pai (verificado nos 15 comentários mais recentes do board em 2026-09-22 — nenhum traz metadado de parent). Definição adotada pelo Giovani: **resposta = comentário novo em card onde a pessoa está adicionada ou já comentou antes**.

Para não avisar duas vezes pelo mesmo comentário, **a menção tem prioridade**: se a pessoa foi mencionada, sai a mensagem de menção e o aviso de resposta é descartado. Um comentário gera no máximo **uma** mensagem por pessoa.

Outras regras do Code `Classificar destinatárias`: autor do comentário nunca é avisado de si mesmo; auto-adição a card não avisa; etiqueta aplicada pela própria Luciana não avisa.

### Tradução do nome da lista

Função `contexto()` (nos dois nós de Code), aplicada ao **nome** da lista:

| Lista | Vira |
|---|---|
| `SETEMBRO \| SEMANA 1`, `AGOSTO SEMANA 4`, `MARÇO \|  SEMANA 03` | `da *Semana N de Mês*` |
| `BLOG` | `do *Blog*` |
| `LINKEDIN` | `do *LinkedIn*` |
| qualquer outra | `em *<nome cru>*` |

Regex tolerante às três grafias (com `|`, sem `|`, espaço duplo) e ao zero à esquerda (`03` → `3`).

## Formato das mensagens

**Tempo real:**

```
🔵 Oi, Thairine!

Você foi adicionada ao card *ID Visual* da *Semana 1 de Setembro*

👤: *Giovani*
🗓: *25/09/2026*
🔗: https://trello.com/c/xxxx
```

Cabeçalhos por tipo: `🔵 ... você foi adicionada ao card *X* <ctx>` · `🟡 ... você tem uma nova menção no card *X* <ctx>` · `🟡 ... você tem uma nova resposta no card *X* <ctx>` · `🟢 ... o card *X* <ctx> foi marcado como *APROVADO*`.

**Agrupada (fila):** saudação `Bom dia, <apelido>!` (a fila só roda de manhã), um bloco por evento, cada um com a bolinha do seu tipo e o timestamp da ocorrência:

```
Bom dia, Lu!

Enquanto você estava fora, chegaram 2 novidades:

🟢 O card *ID Visual* da *Semana 1 de Setembro* foi marcado como *APROVADO*

👤: *Giovani* (*21/09 às 18h40*)
🗓: *25/09/2026*
🔗: https://trello.com/c/xxxx

🟡 Nova menção no card *Agosto* do *Blog*

👤: *Thairine* (*22/09 às 08h12*)
🔗: https://trello.com/c/wwww
```

**Regras de formatação definidas pelo Giovani em 2026-09-22:**
- Saudação em linha própria, separada do resto por linha em branco
- Nome de quem agiu e datas em **negrito**
- **Card sem prazo não mostra a linha 🗓** — some o ícone inteiro, não escreve "sem prazo definido"
- Hora sempre com `h` (`18h40`, não `18:40`)
- `🔗` sem texto ao lado, só o ícone e a URL
- Apelidos: **Thairine** e **Lu**

**Onde editar o texto:** nó `Classificar destinatárias` (tempo real) e `Montar mensagem agrupada` (fila). Nos dois, os blocos editáveis ficam no topo, marcados com `EDITAR AQUI`.

## Fila

**Data Table `fila-trello-libertas`** (id `QCKb3dKnxHvEmGD0`), criada inteira via API pública. Colunas: `card_id`, `destinataria_id`, `tipo`, `por_quem`, `quando`. A coluna `tipo` é a diferença para as filas da Open Mídia — aqui a mesma fila guarda 4 tipos de evento por pessoa.

Comportamentos herdados dos fluxos da Open Mídia: fila vazia às 9h = nenhuma mensagem (normal, não é erro); card apagado é pulado (`onError: continueRegularOutput`); se um envio falhar, a fila **não** é limpa — decisão do Giovani: melhor repetir aviso que perder.

## Descoberta: o payload de `addLabelToCard`

A documentação oficial do Trello **não descreve** o `action.data` de `addLabelToCard` (consultada em 2026-09-22: developer.atlassian.com mostra exemplo só de comentário e voto), e o feed de atividades do board retorna **zero** eventos desse tipo — não dá para espiar o formato pelo histórico. Existe inclusive um [relato de webhook não disparar em etiqueta](https://github.com/trello/api-docs/issues/154).

**Resolvido na prática** (execução 5574, 2026-09-22): o webhook **dispara** e o payload traz o label completo:

```json
"label": { "id": "674a3667021bc52454ce03d8", "name": "APROVADO", "color": "green" },
"text": "APROVADO",
"value": "green"
```

O código aceita `data.label.id` **e** `data.idLabel` por segurança, mas o formato real é o primeiro.

**Achado irmão:** adicionar etiqueta dispara **dois** eventos — `addLabelToCard` e um `updateCard` com `data.card.idLabels`. O filtro de tipos já descarta o segundo; sem isso, a mensagem sairia dobrada.

## Validação (2026-09-22)

- **Em produção:** Giovani criou o card `Teste Gio - 22/09`, aplicou a etiqueta APROVADO às 20h14. Execuções 5573 (`createCard`, barrada no filtro), 5574 (`addLabelToCard`, percorreu até `Guardar na fila`) e 5575 (`updateCard`, barrada no filtro). Linha gravada corretamente na fila; mensagem agrupada conferida por execução manual do ramo da fila.
- **Offline (11 cenários simulados contra o código real do nó):** adição de membro, APROVADO, APROVADO COM ALTERAÇÃO (ignorado), menção às duas, resposta por ser membro, resposta por já ter comentado, menção + membro (sem duplicar), card do Blog sem prazo, lista fixa, `MARÇO |  SEMANA 03`, auto-menção (ignorada). Todos passaram.

## Pendências

- **Números reais no WhatsApp:** os 4 nós Evolution (2 tempo real + 2 fila) apontam para o número do **Giovani** (`5511986501499`) — de propósito, para a fase de teste. Falta trocar pelos números da Thairine e da Luciana.
- **Eventos ainda não testados ao vivo:** adição da Thairine a um card, comentário com menção, comentário sem menção (resposta).
- **Melhoria do Error Workflow** "Alerta de Erro" (mais detalhes: horário, tipo do nó, descrição longa) — vale para todos os fluxos, combinado com o Giovani para depois.

## Conexões

- [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]] — os 6 fluxos que serviram de molde
- [[wiki/systems/n8n.md|n8n]] — API REST, Data Tables, pegadinhas de PUT
- [[wiki/systems/evolution-api.md|Evolution API]] — envio das mensagens (instância `Giobot`)
- [[wiki/tools/trello-mcp.md|Trello MCP (comunidade)]] — credenciais usadas nas consultas ao board
