---
tier: semantic
---
---
type: concept
tags: [ai-memory, hermes, obsidian, wiki]
title: Wiki como Fonte da Verdade
description: A wiki markdown (+ SQLite + FTS5 + Git) é a memória durável. Conhecimento vai pra wiki, não pra memory() do Hermes.
timestamp: 2026-06-17T00:00:00+00:00
---

# Wiki como Fonte da Verdade

## Contexto

A memory() do Hermes tem limite de 2.200 chars. Solução: ai-memory — servidor Rust que indexa markdown em SQLite + FTS5, versionado por Git.

| Camada | Tecnologia | Função |
|---|---|---|
| Fonte da verdade | Markdown puro | Editável no Obsidian, grep, diff, git |
| Índice | SQLite + FTS5 | Busca rápida |
| Versionamento | Git | Histórico, diff, rollback |
| Servidor | Rust | Rápido, baixo consumo |
| Acesso | MCP tools | Hermes consulta/escreve |
| Visual | Web UI + Obsidian | Browser + IDE visual |

## Estrutura real do vault

```
wiki-home.md              → índice raiz
hermes/                   → Hermes Agent
  conceitos/              → conceitos transversais
  decisions/              → decisões de arquitetura
  gotchas/                → armadilhas
  procedures/             → procedimentos
  rules/                  → regras
  sessoes/                → registro de sessões
  todo/                   → próximos passos
hermes-config/            → configuração do Hermes
  notes/                  → notas gerais
  _rules/                 → regras do agente
infra-vps/                → Docker, Swarm, Traefik, VPS
  decisions/
  gotchas/
  procedures/
  rules/
geral/                    → conteúdo não categorizado
  procedures/
  sessoes/
```

## Regras

1. Conhecimento durável vai pra wiki, não pra memory() — toda decisão, gotcha, procedimento, regra vira markdown
2. Memory() do Hermes é cache de sessão (2.200 chars), não fonte da verdade
3. Uma página por conceito — seguir OKF: type, title, description, tags, timestamp
4. Cross-links entre páginas relacionadas usando wikilinks
5. Todo arquivo tem frontmatter OKF
