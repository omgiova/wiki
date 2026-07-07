---
type: system
tags: [evolution-api, whatsapp, n8n, mensageria]
title: Evolution API
description: Gateway de WhatsApp rodando na VPS (Docker Swarm, rede easypanel); instância "Giobot" usada pelo n8n via nó comunitário para enviar mensagens
timestamp: 2026-07-06T22:19:00-03:00
status: draft
---

# Evolution API

## O que é

Gateway de **WhatsApp** self-hosted rodando na VPS. Permite enviar (e receber) mensagens de WhatsApp programaticamente — usado como canal de saída das automações do n8n (ex.: [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]]).

> ⚠️ Página criada a partir do que foi **verificado em uso** em 2026-07-06. Detalhes de deploy (URL, porta, versão, painel) ainda não levantados — completar quando forem verificados.

## Stack e configuração

- Roda como serviço no **Docker Swarm** da VPS, na rede overlay `easypanel` (não está na `easypanel-projetos`) — ver [[wiki/systems/vps.md|VPS]]
- **Instância configurada:** `Giobot` (instância = uma sessão de WhatsApp conectada)
- URL, porta, apikey e método de deploy: **não verificados ainda**

## Interface

- **No n8n:** nó comunitário `n8n-nodes-evolution-api` (instalado no n8n da VPS), credencial **"Evolution account"** cadastrada na UI do n8n
  - Envio de texto: resource `messages-api`, campos `instanceName` (`Giobot`), `remoteJid` (destinatário) e `messageText`
  - **Formato do destinatário (`remoteJid`):** `<DDI><DDD><número>@s.whatsapp.net` — ex.: `5511986501499@s.whatsapp.net`
- API REST própria da Evolution: existe, mas endpoints/URL não verificados neste setup

## Operação

- Uso validado em 2026-07-06: envio de mensagens de texto disparadas por workflow do n8n (webhook do Trello → mensagem no WhatsApp) — funcionou em produção nos testes
- Quem gerencia a instância/conexão do WhatsApp: **não documentado ainda**

## Erros conhecidos

- Nenhum registrado até agora

## Conexões

- [[wiki/systems/n8n.md|n8n]] — principal consumidor (nó comunitário)
- [[wiki/systems/vps.md|VPS]] — host e redes do Swarm
- [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]] — primeiro projeto usando o canal
