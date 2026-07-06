---
type: tool
tags: [n8n, mcp, agentes, automacoes]
title: n8n MCP
description: MCP do n8n — server stdio caseiro que expõe a API do n8n a qualquer agente da VPS; registro nos clientes, env, capabilities e erros conhecidos
timestamp: 2026-07-05T21:53:00-03:00
status: draft
---

# n8n MCP

## O que é

Interface MCP da plataforma [[wiki/systems/n8n.md|n8n]]. Conteúdo movido de `systems/n8n.md` (Interface, bullet MCP), verbatim:

> server stdio em `/root/.hermes/mcp-installs/n8n/server.py` — interface para qualquer agente da VPS; registrado no gateway Hermes e no Claude Code. Ordem de resolução do env: `$N8N_MCP_ENV` → `~/.config/n8n-mcp/env` → `$CWD/.env`

## Capabilities

Ferramentas expostas no Claude Code (conjunto observado em 2026-07-05):

- **Workflows:** `find_workflows`, `list_workflows`, `get_workflow`, `export_workflow`, `activate_workflow`, `deactivate_workflow`
- **Execuções:** `list_executions`, `get_execution`, `recent_failures`
- **Operação:** `health`, `container_logs`

O conjunto exposto no gateway Hermes pode diferir — conferir na skill `n8n-mcp-tools-expert` em `/root/.hermes/skills/`.

## Limites

- O env só é relido no restart do MCP — trocar a chave exige reiniciar o server no cliente
- Edição de workflows via API/MCP segue as regras de ouro documentadas em [[wiki/systems/n8n.md|n8n]] (Operação): o n8n substitui o workflow inteiro no update; nó omitido é perdido

## Como usar

Já registrado no gateway Hermes e no Claude Code — os agentes chamam as ferramentas diretamente. Base de conhecimento: 8 skills `n8n-*` em `/root/.hermes/skills/` (recurso de todos os agentes da VPS).

## Quando não usar

- Operação de infraestrutura do n8n (deploy, scale, 502 do Traefik) — assunto do system [[wiki/systems/n8n.md|n8n]] e da [[wiki/systems/vps.md|VPS]]
- Criação/edição de credenciais de integrações — fazer pela UI do n8n

## Configuração

Credenciais são as mesmas da API REST do n8n (`N8N_API_KEY` + `N8N_BASE_URL`), no path canônico `~/.config/n8n-mcp/env` (`chmod 600`; fonte: `/root/.hermes/.env`) — detalhes em [[wiki/systems/n8n.md|n8n]] (Interface). O MCP resolve o env na ordem: `$N8N_MCP_ENV` → `~/.config/n8n-mcp/env` → `$CWD/.env`.

## Erros conhecidos

Movido de `systems/n8n.md` (Erros conhecidos), verbatim:

> **`N8N_API_KEY is missing` na MCP:** env não encontrado. Gateway Hermes mascara via fallback `$CWD/.env` (cwd `/root/.hermes/`); outros clientes exigem o path canônico. Resolvido 2026-07-02. Env só é relido no restart do MCP.

## Status de validação

Em uso validado desde 2026-07-02 no gateway Hermes e no Claude Code (pendência do `N8N_API_KEY` resolvida — ver [[wiki/systems/n8n.md|n8n]]).

## Conexões

- [[wiki/systems/n8n.md|n8n]] — a plataforma que este MCP expõe (stack, workflows, API, operação)
- [[wiki/systems/hermes.md|Hermes]] — agente que consome o n8n via MCP
- [[wiki/tools/elevenlabs-mcp.md|ElevenLabs MCP]] — outro MCP documentado no mesmo padrão
