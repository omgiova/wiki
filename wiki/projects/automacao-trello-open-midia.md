---
type: concept
tags: [projects, trello, n8n, evolution-api, whatsapp, automacao]
title: Automação Trello — Open Mídia
description: Automações sobre o board "DEMANDAS GERAIS | Open Mídia Digital" — Fluxo 1 (n8n) avisa no WhatsApp quando um membro é adicionado a um card; Fluxo 2 manda lista semanal de prazos por membro. Ambos validados.
timestamp: 2026-07-08T00:20:00-03:00
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
- **Filtro anti-auto-notificação** (adicionado 2026-07-09): o nó `Só addMemberToCard` ganhou uma 2ª condição (combinador `and`) — `action.data.idMember != action.memberCreator.id`. Se o membro se adiciona ao próprio card, o item é descartado ali e nenhuma mensagem é enviada (ele já sabe que se adicionou). Editado via PUT na API REST alterando **só** os `parameters` desse nó; os outros 7 nós, conexões, credenciais e settings foram enviados de volta idênticos ao GET anterior, para não repetir o incidente de sobrescrita das saudações.
- **Nome da lista limpo** (adicionado 2026-07-13): a linha `➡️ Lista:` dos 4 nós Evolution deixou de mostrar o nome cru do Trello (ex.: `3- PATI BONETTI | 3 CONTEÚDOS SEMANA`) e passou a mapear `$json.list.id` → nome limpo, mesma ideia do listMap do Fluxo 2. Diferença: aqui os nomes são em caixa normal (a pedido do Giovani) — `Open Mídia`, `Libertas`, `Jobs Extras`, `Teacher Thais`, `Anderson Nazario`, `Pati Bonetti`, `Domain`, `Informações gerais`, `Arquivado`. Mapa embutido na expressão de cada nó (`{{ ({<id>:'<nome>'})[$json.list.id] || $json.list.name }}`), com fallback pro nome cru se a lista não estiver no mapa. Editado via PUT na API REST alterando **só** o `messageText` desses 4 nós; demais nós, conexões, credenciais e settings devolvidos idênticos ao GET (diff conferido nó a nó; 4 saídas do Switch verificadas após a gravação).

### Horário comercial + fila fora do horário (VALIDADO 2026-07-18)

O fluxo ganhou regra de horário comercial, construída em 2026-07-18 via PUTs na API (resto sempre devolvido verbatim) e **validada pelo Giovani no mesmo dia**. Total: 21 nós.

- **Em tempo real** a mensagem individual só sai **seg–sex, 8h–16h59 (BRT)**: nó Code `Horário comercial?` calcula na timezone `America/Sao_Paulo` e um If `Fora do horário?` decide o caminho.
- **Fora do horário**, o evento vira linha na **Data Table `fila-horario-trello-openmidia`** (id `9x6YWmbkf3dI00Be`; colunas `card_id`, `membro_id`, `adicionado_por`, `quando`) — tabela criada pelo Giovani na UI, colunas criadas via API pública (ver [[wiki/systems/n8n.md|n8n]], seção Data Tables). A fila fica visível na aba "Data tables" da UI.
- **Ramo da fila:** Schedule seg–sex 8h → lê a fila inteira → HTTP busca dados frescos de cada card (`onError: continueRegularOutput` — card apagado é pulado, não trava o envio) → Code agrupa por membro e monta **uma mensagem por pessoa**, cada card com o timestamp da adição — ex.: `📌 *Card X* — por *Giovani* (17/07 às 23h14)` → Switch próprio (`$json.membroId`) → 4 nós Evolution novos (`Fila - <nome>`, mesmos `remoteJid` e credencial dos originais) → `deleteRows` limpa a tabela.
- **Comportamentos:** fila vazia às 8h = nenhuma mensagem (normal, não é erro); se um envio falhar, a fila **não** é limpa — as linhas ficam pra rodada seguinte (pode repetir mensagem já enviada; preferiu-se não perder aviso). O Error Workflow "Alerta de Erro" (`3MI1k15YL5OUrEXF`) foi apontado nas Settings em 2026-07-18 (antes o Fluxo 1 não tinha, diferente dos Fluxos 2 e 4).
- **Mensagens (dois caminhos):** começam com 🟢 e a linha de lista virou `➡️ Cliente:` (edições do Giovani em 2026-07-18). Tempo real **sem** timestamp; só a mensagem agrupada da fila tem.
- **Iteração intermediária:** a primeira versão do mesmo dia usava nó Wait (1 execução dormindo por evento, 1 mensagem por card); foi substituída pela fila com Data Table pra agrupar em mensagem única — decisão do Giovani, descartadas as alternativas de só filtrar o horário (perderia avisos) e de fila externa ao n8n.

### Macetes do payload do webhook (custaram a descobrir)

- `action.member` / `action.data.idMember` = quem **foi adicionado** (usado no Switch)
- `action.memberCreator` = quem **executou a ação** (usado no "por Fulano")
- Nome do card: `action.data.card.name`
- Evento de membro adicionado ao **quadro** (não a card) é outro type: `addMemberToBoard`

## Fluxo 2 — Lista semanal de prazos por membro (funcionando, validado 2026-07-07)

**Workflow n8n:** `Trello Prazos por Membro - Open Mídia` (ID `PRaGCXrFKcmiusfE`), criado via API REST em 2026-07-07, testado com sucesso pelo Manual Trigger (200 cards processados, sem duplicação). **Ativo em produção** desde 2026-07-08, com números reais dos 4 membros e Error Workflow apontado (ver [[wiki/systems/n8n.md|n8n]]). JSON completo desta versão (primeira oficial validada) arquivado em [[raw/fluxo-2-trello-prazos-workflow-2026-07-09.md]].

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

- **Gatilhos:** `Schedule Trigger` semanal (segunda-feira 8h) + `Manual Trigger` de teste, ambos ligados em paralelo direto nos dois nós HTTP (não um atrás do outro — ver bug abaixo).
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

### Como editar o Fluxo 2 (pela UI do n8n, sem precisar de agente)

Diferente do Fluxo 1 (mensagem em expressões diretas nos nós Evolution), a mensagem do Fluxo 2 é montada dentro do nó de **Code** "Filtrar e agrupar por membro" (JavaScript) — precisa disso pra agrupar/ordenar cards. Onde mexer:

- **Horário/dia do envio automático:** dois cliques no nó `Schedule (Segunda 8h)` → campos `Trigger at Hour/Minute` e `Trigger at Days of Week`.
- **Número de WhatsApp de alguém:** dois cliques no nó Evolution da pessoa (ex. `Enviar texto - Gabriele`) → campo `remoteJid`.
- **Apelido usado na saudação, quantidade de dias da janela, e o texto/emoji da mensagem:** dentro do nó `Filtrar e agrupar por membro`, três blocos seguros de editar sem tocar no resto do código:
  1. `const nicknames = { ... }` — troca só o texto entre aspas
  2. `const windowDays = 7;` — troca o número de dias
  3. As linhas com crase (`` ` `` `texto ${variavel} texto` `` `` ``) que montam `header` e `cardLines` — o texto ao redor de `${...}` (emoji, palavras, `\n`, negrito com `*`) é livre; **não apagar os `${...}` nem as crases**
- **Regra de ouro:** se a linha é só texto entre crases, é seguro editar. Se tem `for`, `if`, `.sort(`, `.map(` fora de uma crase, é lógica — não mexer sem entender.
- Incidente real (2026-07-08): o Giovani tentou trocar o apelido "Giovani" → "Gio" direto no Code node e achou que tinha quebrado o fluxo (susto, não bug real) — a estrutura toda (conexões, Switch, Schedule) ficou intacta; só o texto do apelido saiu errado (`"Giov"` em vez de `"Gio"`). Corrigido trocando só essa string.

### Pendências

- [x] Ativar (`active: true`) — feito, workflow em produção desde 2026-07-08
- [x] `remoteJid` dos 4 nós Evolution — números reais dos 4 membros
- [x] Alerta de erro — Error Workflow `Alerta de Erro` (ver [[wiki/systems/n8n.md|n8n]]) apontado nas Settings em 2026-07-09

## Fluxo 3 — Banco de dados de cards em Markdown (v1 VALIDADA em 2026-07-12)

**Workflow n8n:** `Trello Open Mídia - Banco de Dados` (ID `VPIpLm5pujpZvVDY`), inativo (execução manual, card único por URL). Construído e iterado com o Giovani em 2026-07-12 sobre testes reais; **validado por ele no mesmo dia**, com gravação real no GitHub. JSON completo desta versão arquivado em [[raw/fluxo-3-om-database-workflow-2026-07-12.md]].

> ⚠️ **Esta v1 é IMUTÁVEL (regra do Giovani):** o workflow validado não será mais editado. A próxima automação (versão board inteiro) deve **duplicar** o flow no n8n e editar a cópia.

**O que faz:** extrai todos os dados de um card do Trello e gera um arquivo Markdown com frontmatter OKF, commitado direto no repo GitHub **privado `omgiova/om-database`** pelo próprio n8n (credencial "GitHub account" do Giovani). **Não é backup** — é banco de dados de conteúdo, navegável no Obsidian; re-execução sobrescreve o arquivo e o histórico fica nos commits.

```
Manual Trigger → Set "Card de teste (colar URL aqui)" → HTTP "Buscar card completo"
  → Code "Montar markdown (OKF)" → Filter "Só cards válidos"
  → GitHub "Gravar no GitHub (novo)" ──(erro: já existe)──> GitHub "Atualizar no GitHub (já existe)"
```

- **Buscar card completo:** `GET /1/cards/<id>` (id extraído da URL colada), credencial `trelloApi` "Trello account"; `fields=name,desc,due,closed,idList,shortLink,shortUrl,dateLastActivity,labels` + list, members, checklists=all, attachments, actions=commentCard (limit 1000)
- **Create→edit:** o nó "Gravar" usa create com `onError: continueErrorOutput`; se o arquivo já existe, o GitHub recusa e o item desvia pro nó de edit (re-execução = sobrescrita). Commit: `card: <filename>`
- **Cards que NÃO entram** (saem do Code como `ignorado: true`, barrados no Filter): lista "Informações gerais" (`6908c0021d3d2a7086b24430`) e organizadores de semana (nome começando com 📅 ou contendo `SEMANA <n>`)

**Arquivo gerado** — nome `<lista>-<formato>-<assunto-kebab>-<shortLink>.md`, na raiz do repo:

- **Frontmatter (ordem validada):** `type` (lista limpa: `open-midia`, `libertas` — mesma limpeza do Fluxo 2, sem prefixo), `tags: []` (**reservada** pra futura etapa com IA), `etiquetas` (TODAS as etiquetas do card, verbatim, sem filtro), `title` (nome original), `description: ""` (**reservada** pra etapa futura), `timestamp` (BRT), `formato`, `canal`, `lista` (nome original), `membros`, `criado` (data de criação extraída dos 8 primeiros hex do ID do card — o Trello não dá esse campo), `trello_id`, `url`, `status: draft` (por último, a pedido do Giovani)
- **Corpo com seções fixas:** Descrição, Checklists, Comentários, Anexos — sempre presentes, vazias = `_[sem dados]_`. Anexos são **links** (nome + URL), não cópia dos arquivos; capa não é destacada (decisão do Giovani)
- **Parser tolerante do nome do card** (`FORMATO | ASSUNTO`, `DATA | FORMATO | ASSUNTO`, livre): tokens por `|`; data, formato e canal por dicionário fixo; resto vira assunto; fora do padrão não quebra (`formato` vazio)
- **Mapa de formatos** (verificado nos dados do board cruzando nomes × etiquetas): `carrossel`/`C` → carrossel; `estático` → estatico; `reels`/`R`/`RT`/`Reels teste`/`vídeo` → reels; `STORIE`/`stories` → storie. **Conflito nome × etiqueta: nome vence** (etiqueta é só fallback de formato)
- **Canal:** token `Linkedin`/`Instagram` no nome OU etiqueta — sem duplicar. Etiquetas só-cor ignoradas

### Bugs e lições da construção (2026-07-12)

1. **`labels` faltando no `fields`:** a busca do card não pedia `labels` à API — etiquetas chegavam vazias no Code node (e o fallback de formato por etiqueta nunca tinha funcionado). Corrigido adicionando `labels` ao `fields`
2. **Save da UI sobrescreve edições via API:** com o workflow aberto no editor do n8n, o Save da UI gravou a versão antiga da tela por cima de 2 edições feitas via PUT (campos `criado` e `etiquetas` sumiram — pareceu regressão do código, era corrida de versões). Regra prática: **depois de editar via API, recarregar a página do n8n antes de salvar qualquer coisa na UI**
3. Criação do repo GitHub: o token fine-grained da VPS não cria repo nem enxerga repo privado fora da sua lista — o `om-database` foi criado pelo Giovani na UI (com README, pra já nascer com branch `main`, exigida pelo nó do GitHub)

**Próximo passo (não construído):** versão board inteiro — duplicar este flow e trocar a entrada por `GET /1/boards/<id>/cards/all` (todos os cards, inclusive arquivados); o restante do fluxo já processa múltiplos itens naturalmente.

## Fluxo 4 — Banco de dados do board inteiro (VALIDADO 2026-07-12, ativo com rodada semanal)

**Workflow n8n:** `Trello Open Mídia - Banco de Dados auto` (ID `Dl4vDai92a39Cvfy`), **ativo** (Schedule semanal + gatilho manual). Criado pelo Giovani na UI **duplicando o Fluxo 3 v1** (que permanece imutável); a entrada, a fila e o resumo foram construídos via API (PUT) em 2026-07-12.

**Validação:** o Giovani rodou a carga completa do board em 2026-07-12 e **deu tudo certo** — o repo `omgiova/om-database` ficou com todos os cards atuais documentados. No mesmo dia o fluxo ganhou a implementação que faltava desde o início (rodada semanal incremental, ver abaixo) e foi publicado/ativado pelo Giovani. Primeira execução agendada esperada: **segunda 2026-07-13, 7h**.

**O que faz:** roda o mesmo processamento do Fluxo 3 para os cards do board (inclusive arquivados), do mais antigo pro mais novo, gravando um arquivo por card no repo `omgiova/om-database` — e no final manda um resumo no Telegram. Desde a versão semanal, cada rodada processa **só os cards com atividade nos últimos 8 dias** (novos ou editados); como o nome do arquivo é determinístico, reprocessar card já gravado apenas atualiza o arquivo, nunca duplica.

```
Schedule (Segunda 7h) ─┬→ HTTP "Listar cards do board" (GET /1/boards/6908bffbc7473c1134fe279d/cards/all, fields=id,dateLastActivity)
Manual Trigger ────────┘
  → Code "Ordenar do mais antigo pro mais novo" (filtra últimos 8 dias + ordena) → Split in Batches "Fila (1 por vez)"
       → Buscar card completo → Montar markdown (OKF) → If "Card válido?"
            ├─ sim → Gravar no GitHub (novo) ──(erro: já existe)──> Atualizar no GitHub (já existe)
            └─ não → pula o GitHub
       → Wait "Esperar 2s" → volta pra Fila
  → (fila vazia = fim) → Code "Resumo" → Telegram "Resumo no Telegram" (chat do Giovani)
```

**Decisões de desenho (escolhidas pelo Giovani em 2026-07-12):**

- **Fiel ao Fluxo 3 v1:** manteve-se 1 commit por card (nó de GitHub da v1), descartando as alternativas de commits em lote (API de árvore do GitHub) e de gravação em grupos de 10 — a fidelidade ao flow validado pesou mais que eficiência
- **Ordem cronológica de criação:** ordenação pelos IDs dos cards em ordem crescente (a data de criação está embutida no ID — mesma fonte do campo `criado` do frontmatter)
- **Fila anti-falha:** 1 card por vez + Wait de 2s entre cards (~30 gravações/min, folga sobre o limite prático de ~80/min do GitHub; ~200 cards ≈ 10–15 min). Retry on Fail (3 tentativas, 5s) nos dois nós HTTP do Trello e no "Atualizar no GitHub" — sem retry no "Gravar (novo)" de propósito, porque o erro dele é o caminho normal de "arquivo já existe"
- **Término garantido, sem loop infinito:** a lista de cards é buscada uma única vez no início e vira fila fechada; o Split in Batches termina sozinho quando ela acaba. Sem repetição: nome de arquivo determinístico (shortLink), re-execução sobrescreve
- **Filter → If:** o Filter da v1 foi trocado por um If no Fluxo 4 porque, dentro do loop de 1 em 1, um card ignorado morreria no Filter e **travaria a fila no meio** — com o If, o card ignorado desvia do GitHub e volta pra fila
- **Error Workflow:** settings do workflow apontam para "Alerta de Erro" (`3MI1k15YL5OUrEXF`) — configurado no Fluxo 4, sem mexer no fluxo de erro (ele é genérico)
- **Resumo no Telegram** ao final: total de cards no board, ignorados, gravados novos, atualizados (o Code "Resumo" soma os itens de todas as voltas do loop)

### Rodada semanal incremental (implementada via API em 2026-07-12, após a validação da carga completa)

Três mudanças aplicadas por PUT (todo o resto devolvido verbatim; diff conferido nó a nó após a gravação):

1. **Novo nó `Schedule (Segunda 7h)`** ligado na listagem, ao lado do Manual Trigger (que fica pra testes/reprocessamentos)
2. **`Listar cards do board`** passou a pedir `fields=id,dateLastActivity`
3. **Code `Ordenar do mais antigo pro mais novo`** ganhou filtro no início: só passam cards com `dateLastActivity` nos últimos 8 dias (7 + 1 de folga). A janela é editável na linha `const diasJanela = 8;` — mesmo padrão do `windowDays` do Fluxo 2. Pra reprocessar o board inteiro manualmente, basta aumentar temporariamente esse número (ex.: 36500)

Comportamentos a saber:

- O filtro pega cards **novos e editados** na semana (descrição, prazo, comentário, checklist) — o arquivo no repo é atualizado junto
- **Semana sem card mexido = nenhum resumo no Telegram** (o fluxo termina cedo, com 0 itens). Ausência de mensagem na segunda não é erro; erro real dispara o Error Workflow "Alerta de Erro"
- **Limitação conhecida:** card renomeado ou movido de lista muda o nome do arquivo — o novo é criado e o antigo fica órfão no repo; limpar manualmente quando acontecer (decisão de 2026-07-12: não vale complicar o fluxo por isso)
- Corrida de versões UI × API (lição do Fluxo 3) não ocorreu aqui: o Publish do Giovani preservou a edição via API — verificado por GET + diff logo após (13 nós, schedule presente, filtro no lugar)

## Ideias futuras (desenhadas, não construídas)

- **Backup do board em Markdown + git:** exportar JSON do board e explodir em um arquivo por card (frontmatter, descrição, checklists, comentários, wikilinks pra lista/labels/membros), repo git próprio (ex.: `/root/trello-backup/`, fora da wiki) — git diff vira auditoria do que as automações mudaram. Regra anti-estrago: automações nunca usam Delete (só Archive, que é reversível).
- Alarme de eventos destrutivos (`deleteCard` etc.) via mesmo padrão Trigger→Filter.

## Referências

- Tabelas completas de ações das 3 integrações Trello (fora da wiki): `/root/mcp/n8n-trello-node.md`, `/root/mcp/trello-mcp-comunidade-acoes.md`, `/root/mcp/trello-mcp-oficial-acoes.md`

## Conexões

- [[wiki/systems/n8n.md|n8n]] — onde o workflow roda (ver seção Operação: criação de workflows via API REST; e a entrada do Error Workflow "Alerta de Erro")
- [[wiki/systems/evolution-api.md|Evolution API]] — envio de WhatsApp
- [[wiki/tools/trello-mcp.md|Trello MCP (comunidade)]] e [[wiki/tools/trello-mcp-oficial.md|Trello MCP (oficial)]] — acesso ao Trello pelos agentes
- [[raw/fluxo-2-trello-prazos-workflow-2026-07-09.md]] — JSON completo do Fluxo 2, primeira versão oficial em produção
