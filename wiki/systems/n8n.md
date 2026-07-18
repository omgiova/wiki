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

## Erros conhecidos

- **502 via Traefik** quando a tabela IPVS esvazia após scale/update do serviço — ver [[wiki/systems/vps.md|vps]] e case em `/root/.hermes/skills/docker-host-interaction-troubleshooting/`
- Erros específicos da MCP: ver [[wiki/tools/n8n-mcp.md|n8n MCP]]
- Workflow "Teste de skills" com 2 falhas em 03/07 (madrugada) — investigar

## Conexões

- [[wiki/systems/vps.md|VPS]] — host, Swarm, IPVS
- [[wiki/systems/hermes.md|Hermes]] — agente que consome o n8n via MCP
- [[wiki/tools/n8n-mcp.md|n8n MCP]] — interface MCP da plataforma para os agentes da VPS
