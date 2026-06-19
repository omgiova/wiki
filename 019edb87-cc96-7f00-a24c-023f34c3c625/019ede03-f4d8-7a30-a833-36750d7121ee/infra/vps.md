---
tier: semantic
---
---
tags:
- docker
- n8n
- vps
- infra
pinned: true
tier: semantic
---
# Infraestrutura do VPS

## Hardware

- **VPS:** Hostinger KVM 2
- **SO:** Ubuntu 22.04 (Linux 6.8.0)
- **Disco:** 96GB (60GB livre)
- **RAM:** 7.8GB (4.8GB disponivel)

## Servicos rodando

| Servico | Porta | Funcao |
|---|---|---|
| Hermes Agent | 9119 | Dashboard |
| n8n | - | Automacao (MCP) |
| Node-RED | 8800 | Automacao residencial + Alexa |
| ai-memory | 49374 | Memoria agentica |

## Docker Swarm

O VPS roda Docker Swarm (modo cluster single-node).
