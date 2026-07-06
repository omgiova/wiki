---
type: tool
tags: [trello, mcp, agentes, oauth]
title: Trello MCP (oficial Atlassian)
description: MCP oficial do Trello na nuvem da Atlassian (mcp.trello.com) com OAuth; registrado no Claude Code mas bloqueado — Giovani é só convidado no board, e o OAuth exige ser membro do workspace
timestamp: 2026-07-05T22:05:00-03:00
status: draft
---

# Trello MCP (oficial Atlassian)

## O que é

MCP oficial do Trello, hospedado na nuvem da Atlassian: `https://mcp.trello.com/v1`. Nada roda na VPS — cada cliente adiciona a URL e autoriza via OAuth 2.0 pelo navegador (tela de consentimento por workspace). [Documentação oficial](https://support.atlassian.com/trello/docs/connect-trello-to-ai-assistants-with-trello-mcp/).

## Capabilities

No lançamento (2026): boards (ver/criar), listas (ver/mover), cards (ver/criar/atualizar/mover/arquivar/concluir, labels), checklists (ver/criar/atualizar), busca por keyword, Inbox e Planner (eventos de calendário, focus time — este último exige Trello Premium).

**Anunciados como "em breve":** comentários, anexos, campos customizados, histórico de atividade, gestão de labels/membros, copiar cards.

## Limites

- **1 workspace por conexão** (multi-workspace prometido para o futuro)
- **Sem deletes permanentes** — só arquivamento (proteção de fábrica)
- **OAuth não é compartilhável entre clientes:** Claude, Hermes e n8n precisam cada um da própria autorização; a conexão de um não serve pro outro
- **Não aceita API key/token** — só OAuth via navegador (confirmado no FAQ da Atlassian)
- Menos capabilities que o [[wiki/tools/trello-mcp.md|MCP da comunidade]] (sem comentários/anexos/campos customizados por enquanto)

## Como usar

Registrado no **Claude Code** (escopo user, `/root/.claude.json`) desde 2026-07-05. Em 2026-07-06 foi renomeado de `trello` para `trello-oficial`, liberando o nome `trello` para o [[wiki/tools/trello-mcp.md|MCP da comunidade]]:

```
claude mcp add --transport http --scope user trello-oficial https://mcp.trello.com/v1
```

Status: `Needs authentication` — autorizar via `/mcp` na sessão do Claude Code quando o acesso existir (ver Erros conhecidos).

## Quando não usar

- Enquanto o usuário for só **convidado** no board (situação atual) — usar o [[wiki/tools/trello-mcp.md|MCP da comunidade]]
- Quando precisar de comentários, anexos ou campos customizados — ainda não suportados no oficial

## Configuração

Fluxo OAuth: adicionar a URL no cliente → iniciar conexão → login no Trello pelo navegador → escolher o workspace → aprovar permissões (Read, Write, Search). Pré-requisito descoberto na prática: **ser membro do workspace** — ser convidado de um board não basta.

## Erros conhecidos

- **"No Trello workspaces found"** (2026-07-05): a tela de consentimento não lista nada quando a conta não é membro de nenhum workspace. Causa no nosso caso: Giovani é só convidado no board principal. Saídas: (a) dono do workspace adicioná-lo como membro do workspace; (b) usar o MCP da comunidade (adotado); (c) criar workspace próprio (só resolve para boards próprios).
- Conta Atlassian ≠ conta Trello também causa o mesmo erro (e-mails diferentes no login OAuth) — descartado no nosso caso.

## Status de validação

**Bloqueado, não testado.** Registrado no Claude Code, autorização impossível enquanto Giovani não for membro do workspace. Plano: Giovani vai tentar obter as credenciais/acesso futuramente para testar. O registro foi mantido de propósito para esse dia.

## Conexões

- [[wiki/tools/trello-mcp.md|Trello MCP (comunidade)]] — a alternativa em uso, que funciona com API key + token da dona do workspace
- [[wiki/systems/hermes.md|Hermes]] — cliente futuro (precisará da própria autorização OAuth)
