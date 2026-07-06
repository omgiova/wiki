---
type: concept
tags: [mcp, agentes, registro]
title: Registro central de MCPs
description: Registro oficial dos MCPs da VPS — quais existem, link para a página própria de cada um e em quais agentes cada um está registrado
timestamp: 2026-07-06T18:49:00-03:00
status: stable
---

# Registro central de MCPs

Fonte da verdade sobre os MCPs disponíveis na VPS. Detalhes de cada MCP (comando, credenciais, capabilities, erros) ficam na **página própria** dele em `tools/` — aqui fica só o mapa: o que existe e onde está registrado.

## Princípios

- MCPs são instalados **uma vez** no sistema e **registrados** na config de cada agente que precisa deles — nunca reinstalar/duplicar.
- MCPs são recursos da VPS toda, não de um agente específico.
- Todo MCP tem página própria em `tools/` (`<nome>-mcp.md`).

## Registro por agente

- **Hermes:** bloco em `mcp_servers:` no `/root/.hermes/config.yaml` (mudanças só valem após restart do gateway — **nunca reiniciar sem autorização explícita**)
- **Claude Code:** `claude mcp add <nome> -s user -- <comando>`
- **n8n:** preferir os nós nativos do n8n quando existirem, em vez de MCP dentro de workflow

## MCPs da VPS

| MCP | Página | Registrado em | Status |
|---|---|---|---|
| n8n | [[wiki/tools/n8n-mcp.md\|n8n MCP]] | Hermes, Claude Code | ativo |
| ElevenLabs | [[wiki/tools/elevenlabs-mcp.md\|ElevenLabs MCP]] | Hermes | ativo |
| Trello (comunidade) | [[wiki/tools/trello-mcp.md\|Trello MCP]] | Claude Code, Hermes (aguardando restart do gateway) | credenciais validadas 2026-07-06 |
| Trello (oficial Atlassian) | [[wiki/tools/trello-mcp-oficial.md\|Trello MCP oficial]] | Claude Code (OAuth pendente) | bloqueado — exige ser membro do workspace |
| ai-memory | — (`http://127.0.0.1:49374/mcp`) | — | disabled — não usar |

Instalar MCP novo = criar a página própria em `tools/`, adicionar linha nesta tabela e registrar nos agentes necessários.

## Conexões

- [[wiki/systems/hermes.md|Hermes]] — agente que consome MCPs via `config.yaml`
- [[wiki/tools/n8n-mcp.md|n8n MCP]] · [[wiki/tools/elevenlabs-mcp.md|ElevenLabs MCP]] · [[wiki/tools/trello-mcp.md|Trello MCP (comunidade)]] · [[wiki/tools/trello-mcp-oficial.md|Trello MCP (oficial)]]
