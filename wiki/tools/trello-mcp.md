---
type: tool
tags: [trello, mcp, agentes]
title: Trello MCP (comunidade)
description: MCP da comunidade (@delorenj/mcp-server-trello) rodando local via npx com credenciais da dona do workspace; wrapper e env em /root/mcp/, skill em /root/.hermes/skills/trello/; credenciais validadas 2026-07-06
timestamp: 2026-07-06T18:49:00-03:00
status: draft
---

# Trello MCP (comunidade)

## O que é

Servidor MCP da comunidade para o Trello — pacote npm `@delorenj/mcp-server-trello` ([repositório](https://github.com/delorenj/mcp-server-trello)), rodando localmente na VPS via `npx` (Node v22, sem instalação permanente). Escolhido em vez do [[wiki/tools/trello-mcp-oficial.md|MCP oficial]] porque, na época (2026-07-05), o Giovani era apenas **convidado** no board principal (não membro do workspace), o que bloqueava o OAuth do oficial. Este servidor usa API key + token da **dona do workspace**, que enxergam tudo que a conta dela enxerga. Desde 2026-07-06 o bloqueio não existe mais (Giovani virou membro e o oficial foi autenticado); os dois MCPs coexistem com identidades distintas — este age como a dona do workspace, o oficial age como o Giovani.

## Capabilities

~30 ferramentas (referência completa: `references/trello-mcp/api.md` na skill):

- **Boards/workspaces:** listar, criar, definir board/workspace ativo (persiste em `~/.trello-mcp/config.json`)
- **Cards:** criar, atualizar, mover, arquivar, buscar por lista, `get_card` completo (markdown opcional)
- **Checklists:** criar itens, atualizar, buscar por nome/descrição, percentual de conclusão
- **Comentários:** adicionar, editar, apagar, listar
- **Anexos:** imagens e arquivos via URL ou path local (`file://`)
- **Campos customizados:** ler definições e setar valores (exige Trello Standard+)
- **Rate limiting embutido** (limites da API do Trello respeitados automaticamente)

## Limites

- **Tudo sai em nome da dona do workspace** — cards, comentários e ações aparecem como dela, porque as credenciais são da conta dela
- O token dá acesso total à conta dela (scope read,write, sem expiração) — valor sensível
- Board ativo é **compartilhado entre os agentes** (mesmo `~/.trello-mcp/config.json` para Claude e Hermes rodando como root): um agente trocar o board ativo afeta o outro
- Datas: `dueDate` em ISO 8601 com hora; `start` só `YYYY-MM-DD` (convenção da API do Trello)

## Como usar

- **Wrapper:** `/root/mcp/trello-mcp.sh` — lê as credenciais de `/root/mcp/trello.env` e sobe o servidor via `npx -y @delorenj/mcp-server-trello`. É o comando que qualquer cliente MCP da VPS deve apontar.
- **Skill:** `/root/.hermes/skills/trello/` — pacote `skill/` do repositório (clonado em 2026-07-05), com `SKILL.md`, `references/trello-mcp/` (api, patterns, gotchas, configuration), `assets/source/` e `scripts/install.sh`. Recurso de todos os agentes da VPS.
- **Registro nos clientes:** Claude Code (`claude mcp add trello -s user -- /root/mcp/trello-mcp.sh`) e Hermes (bloco `trello` em `mcp_servers:` no `config.yaml` — auto-recarregado, sem restart; `/reload-mcp` se necessário). Ver [[wiki/concepts/mcps.md|Registro central de MCPs]].

## Quando não usar

- **n8n:** usar o nó nativo do Trello do próprio n8n, com a credencial (mesma API key + token) cadastrada na UI (Credentials → Trello API) — caminho natural do n8n, mais confiável que MCP dentro de workflow. Ver [[wiki/systems/n8n.md|n8n]].
- Quando a ação deve sair em nome do **Giovani** (não da dona do workspace), ou quando precisar de Inbox, Planner ou busca por texto — usar o [[wiki/tools/trello-mcp-oficial.md|MCP oficial]] (autenticado desde 2026-07-06; OAuth, sem token guardado, sem deletes permanentes).

## Configuração

- **Credenciais:** `/root/mcp/trello.env` (`chmod 600`, só root) — `TRELLO_API_KEY` + `TRELLO_TOKEN`, da conta da dona do workspace. O "Secret" da página de API do Trello **não é usado** (serve só para OAuth 1.0 e assinatura de webhooks).
- **Geração do token:** a dona abre, logada, `https://trello.com/1/authorize?expiration=never&name=VPS%20Gio&scope=read,write&response_type=token&key=<API_KEY>` → Permitir → copia o token (começa com `ATTA`).
- Opcionais suportados pelo servidor: `TRELLO_WORKSPACE_ID`, `TRELLO_ALLOWED_WORKSPACES` (restringe workspaces acessíveis), `https_proxy`.

## Erros conhecidos

- **`invalid key`** — valor errado no campo da key (ex.: Secret colado no lugar). A API key correta é a da página https://trello.com/app-key da conta da dona.
- **`invalid token`** — key válida, mas token ausente/errado. Tokens atuais começam com `ATTA`; o Secret não funciona como token.
- Teste rápido de credenciais: `source /root/mcp/trello.env && curl -s "https://api.trello.com/1/members/me?key=${TRELLO_API_KEY}&token=${TRELLO_TOKEN}&fields=username"`

## Status de validação

**Credenciais validadas e primeiras tools testadas na prática.** Em 2026-07-05: API key validada contra a API (`invalid token` = key aceita). Em 2026-07-06: token gerado pela dona e validado via curl — `/1/members/me` retorna a conta dela (`lemosluciana`) e `/1/members/me/boards` lista os quadros do workspace. Registrado no Claude Code (✔ conectado) e no `config.yaml` do Hermes — já aparece habilitado no dashboard via auto-reload do config, sem restart. Ainda em 2026-07-06, em sessão do Claude Code: `list_workspaces` e `list_boards` executadas com sucesso (localizaram o board "DEMANDAS GERAIS | Open Mídia Digital" no workspace "Criação - Clientes"). Atenção: o retorno de `list_boards` é gigante (~1,4M caracteres) — estourou o limite de output e precisou ser lido via arquivo salvo. Falta: testar as tools de escrita → adaptação/uso da skill → teste do Giovani em nova sessão. Lista completa das tools realmente expostas: `/root/mcp/trello-mcp-comunidade-acoes.md` (a referência da skill lista 6 tools que o servidor atual não expõe).

## Conexões

- [[wiki/tools/trello-mcp-oficial.md|Trello MCP (oficial)]] — a alternativa da Atlassian, bloqueada por falta de acesso de membro no workspace
- [[wiki/systems/n8n.md|n8n]] — usará o nó nativo do Trello, não este MCP
- [[wiki/tools/n8n-mcp.md|n8n MCP]] e [[wiki/tools/elevenlabs-mcp.md|ElevenLabs MCP]] — outros MCPs documentados no mesmo padrão
- [[wiki/systems/hermes.md|Hermes]] — agente que consumirá este MCP
