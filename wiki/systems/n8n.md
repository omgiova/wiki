---
type: system
tags: [n8n, automacoes, docker, swarm]
title: n8n
description: Plataforma de automação da VPS — Docker Swarm em queue mode via EasyPanel, 24 workflows (5 ativos), API + MCP para agentes
timestamp: 2026-07-02T22:55:00-03:00
status: stable
---

# n8n

## O que é

Plataforma de automação de workflows da VPS. Centraliza integrações Telegram, Spotify, ClickUp, Node-RED e crons de agentes.

## Stack e configuração

Roda no Docker Swarm (EasyPanel, projeto `projetos`), imagem `n8nio/n8n:latest`, em **queue mode**:

| Serviço | Réplicas | Papel |
|---|---|---|
| `projetos_n8n_editor` | 1 | UI + REST API |
| `projetos_n8n_webhook` | 2 | Recepção de webhooks |
| `projetos_n8n_worker` | 1 | Execução dos jobs |

Roteamento: Traefik (443) → DNS do Swarm → IPVS → container (porta interna 5678).

## Interface

- **UI/API:** `https://projetos-n8n-editor.igkokh.easypanel.host` (REST em `/api/v1`)
- **Credenciais:** `N8N_API_KEY` + `N8N_BASE_URL` em `~/.config/n8n-mcp/env` (path canônico, `chmod 600`; fonte: `/root/.hermes/.env`)
- **MCP:** ver [[wiki/tools/n8n-mcp.md|n8n MCP]] — página própria da ferramenta (server, registro nos clientes, env, capabilities e erros)

## Workflows (2026-07-02)

**Ativos (6):**

| Workflow | ID | Função |
|---|---|---|
| Telegram Spotify | `mu8SLtqvLfXl8uvk` | — |
| Telegram ClickUp | `OD7dKauHhZQmI91B` | — |
| Telegram Node-red | `sqvE7a2PH85iKggx` | — |
| Lembretes | `AIuS4W24Ka88kDP6` | — |
| Hermes Cron - Teste | `2jq12kdGs92osySs` | — |
| Alerta de Erro | `3MI1k15YL5OUrEXF` | Error Workflow genérico (Telegram) — qualquer workflow pode apontar pra ele em Settings → Error Workflow |

**Inativos (19):** testes e experimentos (`herminho1–4`, `teste-hermes`, `My workflow 1–3`, `skills 2`, `Teste de skills`, etc.).

## Operação

> ⚠️ **Webhook de gatilho — vale pra toda edição de fluxo (2026-07-20).** Ativar/desativar um workflow reinstala o webhook na origem (Trello); em queue mode isso pode furar a entrega (eventos sem execution e sem erro). **Editar no estado atual via PUT — nunca desativar→reativar só pra editar.** Editar nó comum não mexe no webhook; editar o **nó de gatilho** (ou toggle inevitável) exige conferir depois: `GET /1/tokens/<token>/webhooks` deve ter um `active:true` com `callbackURL` do fluxo.

Regras de ouro para editar workflows via API/MCP (base: skill `n8n-workflow-builder`):

1. Sempre `get_workflow` antes de `update_workflow` — o n8n substitui o workflow inteiro; nó omitido é perdido
2. Erro 400 → ler `error` e `hint` da resposta
3. Cada nó exige `id` UUID v4 único e `position`

### Base de conhecimento: 8 skills `n8n-*` em `/root/.hermes/skills/`

Recurso de todos os agentes da VPS. Mapa do que cada uma cobre (consultar a skill certa **antes** de mexer no assunto dela):

| Skill | Cobre |
|---|---|
| `n8n-workflow-builder` | criar/editar workflows via MCP tools (create/update/get/execute/activate etc.) |
| `n8n-workflow-patterns` | arquiteturas prontas: webhook, integração HTTP/API, banco de dados, IA, batch, agendados |
| `n8n-node-configuration` | parâmetros por nó e operação; campos obrigatórios; **Storage Nodes → nó Data Table** (armazenamento nativo persistente, aba "Data tables" da UI) — referência verificada ao vivo em 2026-04-08, inclui pegadinha do `deleteRows` (não `delete`) |
| `n8n-code-javascript` | Code node em JS: `$input`/`$json`/`$node`, DateTime, loops com SplitInBatches, agregação |
| `n8n-code-python` | Code node em Python (usar só se JS não servir; JS é o recomendado em ~95% dos casos) |
| `n8n-expression-syntax` | expressões `{{ }}`, referência a dados de outros nós, erros comuns de mapeamento |
| `n8n-validation-expert` | interpretar erros/warnings de validação; quais warnings são falsos positivos |
| `n8n-mcp-tools-expert` | formatos e patterns das tools do MCP n8n; consultar antes de chamar qualquer tool |

### Recursos para construir workflows (além do MCP e das skills acima)

- **Referências em `/root/mcp/`** (fora da wiki): `n8n-trello-node.md` (nó nativo Trello do n8n — recursos e operações completos, fonte: doc oficial + código-fonte, 2026-07-06); `trello-mcp-comunidade-acoes.md` e `trello-mcp-oficial-acoes.md` (tabelas de ações dos dois MCPs de Trello)
- **Skill `trello`** em `/root/.hermes/skills/` — MCP de Trello da comunidade (boards, cards, checklists, labels, membros)
- **Exemplos validados no próprio setup:** JSONs completos de workflows em produção arquivados em `raw/` da wiki (Fluxos 2 e 3 do projeto [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]]) — modelos do padrão Trello → Code → Switch → Evolution; a página do projeto reúne as lições reais de construção (PUT verbatim, HTTP roda 1x por item, Switch com saídas mortas, corrida UI × API)
- **Externos:** templates da comunidade (n8n.io/workflows) e doc oficial (docs.n8n.io)

### Criar/editar workflows via API REST (verificado 2026-07-06)

O MCP do n8n não cria workflows — criação/edição é via API REST (`$N8N_BASE_URL/api/v1/workflows`, header `X-N8N-API-KEY`; credenciais em `~/.config/n8n-mcp/env`). Pegadinhas reais encontradas:

1. `POST /workflows` e `PUT /workflows/<id>` aceitam **só** `name`, `nodes`, `connections`, `settings` — campos extras dão 400
2. Em `settings`, só chaves permitidas (`executionOrder`, `saveData*`, `timezone`, etc.) — o GET devolve chaves a mais (ex.: `binaryMode`) que precisam ser removidas antes do PUT, senão: `settings must NOT have additional properties`
3. **Credenciais não são acessíveis via API** (segurança) — criar o workflow com os nós prontos e o usuário seleciona a credencial no dropdown da UI
4. `active` é read-only no corpo — ativar/desativar é por endpoint próprio (ou UI/MCP)
5. Workflow criado via API nasce desativado — bom padrão: criar desativado pro usuário revisar antes de ligar
6. Caso real completo: workflow `Teste-Trello-Membro-Adicionado` em [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]]

### Data Tables (verificado 2026-07-18)

O n8n da VPS tem **Data Tables** nativas — armazenamento persistente por projeto, visível e editável na aba "Data tables" da UI. Uso: buffers/filas, deduplicação, contadores, estado entre execuções, sem banco externo. Referência completa do **nó** (`n8n-nodes-base.dataTable`, operações, filtros, pegadinha do `deleteRows`): skill `n8n-node-configuration` → `OPERATION_PATTERNS.md` → Storage Nodes.

**A API pública também expõe Data Tables** (a skill não sabia disso; verificado ao vivo em 2026-07-18, mesma autenticação da API de workflows):

- `GET /api/v1/data-tables` — lista tabelas (id, nome, projectId, colunas)
- `GET /api/v1/data-tables/<id>` — detalhe de uma tabela
- `POST /api/v1/data-tables/<id>/columns` com `{"name": "...", "type": "string|date|number|boolean"}` — cria coluna (usado pra criar as 4 colunas da tabela `fila-horario-trello-openmidia`, id `9x6YWmbkf3dI00Be`)
- `POST /api/v1/data-tables` com `{"name": "...", "columns": [{"name": "...", "type": "..."}, ...]}` — **cria a tabela inteira** (com colunas) num request só, sem precisar da UI (verificado 2026-07-18 criando a `fila-mencoes-trello-openmidia`, id `66w7MT67Dvp5LOqY`, do Fluxo 5 da automação Trello)

**Limitação do Simple Memory em queue mode** (doc oficial, não testado aqui): o sub-nó Simple Memory (memória de chat dos AI Agents) **não funciona em workflow ativo de produção quando o n8n roda em queue mode** — chamadas podem cair em workers diferentes — e a memória dele é só do processo (restart apaga). Como o n8n da VPS é queue mode, não usar Simple Memory em produção; pra estado persistente, usar Data Tables. Fonte: docs.n8n.io (common issues do nó, consultado 2026-07-18).

### Nós da comunidade — persistência via bind mount (corrigido 2026-09-17)

O n8n instala nós da comunidade como arquivos em `/home/node/.n8n/nodes`. Em Docker essa pasta vive **dentro do container**: toda atualização da imagem (`n8nio/n8n:latest`) recria o container do zero e a instalação é perdida. A doc oficial confirma — *"you may lose the packages when you recreate your container or upgrade your n8n version"* — e manda **persistir o conteúdo de `~/.n8n/nodes`** (*"This is the best option"*).

**Correção aplicada (2026-09-17):** bind mount único compartilhado pelos 3 serviços.

| Campo | Valor |
|---|---|
| Host path | `/etc/easypanel/projects/projetos/n8n-nodes` (dono `1000:1000` = user `node`) |
| Mount path | `/home/node/.n8n/nodes` |
| Serviços | `n8n_editor`, `n8n_worker`, `n8n_webhook` (os mesmos valores nos 3) |

Pasta compartilhada = instala/atualiza **uma vez** e os 4 containers enxergam (o `webhook` tem 2 réplicas, mas é 1 serviço no painel).

**Por que bind mount e não "Volume" do EasyPanel:** o Volume do EasyPanel é criado **por serviço** (`/etc/easypanel/projects/<projeto>/<serviço>/volumes/<nome>`), então o mesmo nome em 3 serviços gera 3 pastas distintas — exigiria 3 instalações e sincronia manual. Só o bind mount permite apontar os 3 para a mesma pasta.

**Alternativas descartadas, com motivo:**

- `N8N_REINSTALL_MISSING_PACKAGES=true` — documentada, mas a própria doc avisa que aumenta o tempo de boot e *"may cause health checks to fail"*; pior, **sem volume** ela reinstala e depois detecta cópia duplicada, quebrando com `nodes package is already loaded` ([issue #14712](https://github.com/n8n-io/n8n/issues/14712), fechado como *not planned*; ver também [#5501](https://github.com/n8n-io/n8n/issues/5501) e [#16685](https://github.com/n8n-io/n8n/issues/16685))
- **Imagem Docker própria** com o nó assado dentro — recomendação original da equipe do n8n para queue mode, e a mais robusta; descartada aqui porque congela o n8n na versão buildada (update passa a exigir rebuild manual), e o Giovani quer o n8n sempre atualizado sozinho
- **Fixar a versão da imagem** (sair do `latest`) — descartado pelo Giovani: atualizar não é o defeito, o defeito era a instalação não sobreviver à atualização

**Instalar/atualizar nó daqui em diante:** continua pela UI (Settings → Community nodes), que escreve nessa mesma pasta. A doc diz que queue mode exige instalação manual via `npm` — na prática a UI funciona nesta instância. **Depois de atualizar um nó, implantar os 3 serviços**: o worker e os webhooks só releem a pasta ao iniciar, senão a UI mostra a versão nova e o fluxo executa a antiga. Atualização do **n8n** em si não exige mais nada.

> Terminologia da doc: *"manual installation"* = linha de comando (`npm install` dentro do container). Instalar pela tela Settings → Community nodes é *GUI installation*, mesmo sendo feito à mão.

## Erros conhecidos

- **Nó da comunidade some após update do n8n** → `Unrecognized node type: <pacote>` e falha de ativação em cascata. Causa: pasta de nós não persistida. **Corrigido em 2026-09-17** pelo bind mount acima. Caso real: `n8n-nodes-evolution-api` sumiu no update automático de 2026-09-16 23h20, derrubando 6 workflows (4 deles dispararam o Alerta de Erro)
- **502 via Traefik** quando a tabela IPVS esvazia após scale/update do serviço — ver [[wiki/systems/vps.md|vps]] e case em `/root/.hermes/skills/docker-host-interaction-troubleshooting/`
- Erros específicos da MCP: ver [[wiki/tools/n8n-mcp.md|n8n MCP]]
- Workflow "Teste de skills" com 2 falhas em 03/07 (madrugada) — investigar

## Conexões

- [[wiki/systems/vps.md|VPS]] — host, Swarm, IPVS
- [[wiki/systems/hermes.md|Hermes]] — agente que consome o n8n via MCP
- [[wiki/tools/n8n-mcp.md|n8n MCP]] — interface MCP da plataforma para os agentes da VPS
