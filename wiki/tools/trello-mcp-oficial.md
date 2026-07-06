---
type: tool
tags: [trello, mcp, agentes, oauth]
title: Trello MCP (oficial Atlassian)
description: MCP oficial do Trello na nuvem da Atlassian (mcp.trello.com) com OAuth; autenticado no Claude Code em 2026-07-06 como Giovani (que virou membro do workspace); 15 tools incluindo Inbox, Planner e busca
timestamp: 2026-07-06T20:41:00-03:00
status: draft
---

# Trello MCP (oficial Atlassian)

## O que é

MCP oficial do Trello, hospedado na nuvem da Atlassian: `https://mcp.trello.com/v1`. Nada roda na VPS — cada cliente adiciona a URL e autoriza via OAuth 2.0 pelo navegador (tela de consentimento por workspace). [Documentação oficial](https://support.atlassian.com/trello/docs/connect-trello-to-ai-assistants-with-trello-mcp/).

## Capabilities

No lançamento (2026): boards (ver/criar), listas (ver/mover), cards (ver/criar/atualizar/mover/arquivar/concluir, labels), checklists (ver/criar/atualizar), busca por keyword, Inbox e Planner (eventos de calendário, focus time — este último exige Trello Premium).

**Anunciados como "em breve":** comentários, anexos, campos customizados, histórico de atividade, gestão de labels/membros, copiar cards.

**Verificado na prática (2026-07-06):** o servidor expõe 15 tools agrupadas (7 de leitura, 6 de escrita, `trelloSearch` e `trelloReadMember`), cada uma com campo `action` que escolhe a operação. IDs no formato ARI da Atlassian; escrita exige ARI obtido por leitura prévia. Exclusividades sobre o [[wiki/tools/trello-mcp.md|MCP da comunidade]] e o nó do n8n: **Inbox** (captura rápida pessoal), **Planner** (eventos de calendário e vínculo card↔evento), **busca por texto livre** com qualificadores, `mark_done` e criação de board como template. Referência completa das 15 tools: `/root/mcp/trello-mcp-oficial-acoes.md`.

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

Status: **autenticado e conectado** desde 2026-07-06, em nome do Giovani (`giovanigomesdeamorim`). O fluxo usado: o próprio servidor expôs tools `authenticate`/`complete_authentication` na sessão do Claude Code — a `authenticate` gera a URL OAuth, o usuário autoriza no navegador e a conexão completa sozinha (o navegador fica "carregando eternamente" no callback `localhost:3118`; é esperado e não impede a conclusão). Alternativa: `/mcp` na sessão.

Identidades distintas entre os MCPs: ações via `trello-oficial` saem em nome do **Giovani**; via [[wiki/tools/trello-mcp.md|MCP da comunidade]] saem em nome da dona do workspace.

## Quando não usar

- ~~Enquanto o usuário for só **convidado** no board~~ — resolvido: Giovani virou membro do workspace em 2026-07-06
- Quando precisar de comentários, anexos, gestão de membros, criar/editar labels, deletes ou campos customizados — ainda não suportados no oficial; usar o [[wiki/tools/trello-mcp.md|MCP da comunidade]] ou o nó do n8n
- Quando a ação deve sair em nome da dona do workspace (e não do Giovani) — usar o MCP da comunidade

## Configuração

Fluxo OAuth: adicionar a URL no cliente → iniciar conexão → login no Trello pelo navegador → escolher o workspace → aprovar permissões (Read, Write, Search). Pré-requisito descoberto na prática: **ser membro do workspace** — ser convidado de um board não basta.

## Erros conhecidos

- **"No Trello workspaces found"** (2026-07-05): a tela de consentimento não lista nada quando a conta não é membro de nenhum workspace. Causa no nosso caso: Giovani era só convidado no board principal. Saídas: (a) dono do workspace adicioná-lo como membro do workspace; (b) usar o MCP da comunidade (adotado); (c) criar workspace próprio (só resolve para boards próprios). **Resolvido em 2026-07-06 pela saída (a)** — Giovani foi adicionado como membro e o OAuth passou a funcionar.
- **Navegador "carregando eternamente" após aprovar o OAuth** (2026-07-06): o redirect vai para `localhost:3118` — que existe na VPS, não na máquina do usuário. Não é falha: a autorização completa mesmo assim (verificar se as tools apareceram antes de repetir o fluxo).
- Conta Atlassian ≠ conta Trello também causa o mesmo erro (e-mails diferentes no login OAuth) — descartado no nosso caso.

## Status de validação

**Autenticado e validado (2026-07-06).** Histórico: ficou bloqueado de 2026-07-05 até 2026-07-06 porque Giovani era só convidado no board; ao virar membro do workspace, o OAuth foi autorizado na sessão do Claude Code e a conexão validada na prática — `trelloReadMember` (`get_me`) retornou a conta do Giovani (`giovanigomesdeamorim`). As demais tools ainda não foram testadas uma a uma. As 15 tools estão documentadas em `/root/mcp/trello-mcp-oficial-acoes.md`.

## Conexões

- [[wiki/tools/trello-mcp.md|Trello MCP (comunidade)]] — a alternativa em uso, que funciona com API key + token da dona do workspace
- [[wiki/systems/hermes.md|Hermes]] — cliente futuro (precisará da própria autorização OAuth)
