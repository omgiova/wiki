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
- **MCP:** server stdio em `/root/.hermes/mcp-installs/n8n/server.py` — interface para qualquer agente da VPS; registrado no gateway Hermes e no Claude Code. Ordem de resolução do env: `$N8N_MCP_ENV` → `~/.config/n8n-mcp/env` → `$CWD/.env`

## Workflows (2026-07-02)

**Ativos (5):**

| Workflow | ID | Função |
|---|---|---|
| Telegram Spotify | `mu8SLtqvLfXl8uvk` | — |
| Telegram ClickUp | `OD7dKauHhZQmI91B` | — |
| Telegram Node-red | `sqvE7a2PH85iKggx` | — |
| Lembretes | `AIuS4W24Ka88kDP6` | — |
| Hermes Cron - Teste | `2jq12kdGs92osySs` | — |

**Inativos (19):** testes e experimentos (`herminho1–4`, `teste-hermes`, `My workflow 1–3`, `skills 2`, `Teste de skills`, etc.).

## Operação

Regras de ouro para editar workflows via API/MCP (base: skill `n8n-workflow-builder`):

1. Sempre `get_workflow` antes de `update_workflow` — o n8n substitui o workflow inteiro; nó omitido é perdido
2. Erro 400 → ler `error` e `hint` da resposta
3. Cada nó exige `id` UUID v4 único e `position`

Base de conhecimento completa: 8 skills `n8n-*` em `/root/.hermes/skills/` (expressões, nós Code JS/Python, padrões, validação, MCP tools) — recurso de todos os agentes da VPS.

## Erros conhecidos

- **502 via Traefik** quando a tabela IPVS esvazia após scale/update do serviço — ver [[wiki/systems/vps.md|vps]] e case em `/root/.hermes/skills/docker-host-interaction-troubleshooting/`
- **`N8N_API_KEY is missing` na MCP:** env não encontrado. Gateway Hermes mascara via fallback `$CWD/.env` (cwd `/root/.hermes/`); outros clientes exigem o path canônico. Resolvido 2026-07-02. Env só é relido no restart do MCP.
- Workflow "Teste de skills" com 2 falhas em 03/07 (madrugada) — investigar

## Conexões

- [[wiki/systems/vps.md|VPS]] — host, Swarm, IPVS
- [[wiki/systems/hermes.md|Hermes]] — agente que consome o n8n via MCP
