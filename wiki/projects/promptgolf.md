---
type: concept
tags: [projects, promptgolf, php, sqlite, opencode-go, easypanel, jogos]
title: Prompt Golf (pt-BR)
description: Tradução pt-BR do jogo promptgolf (holyphoton) — PHP + SQLite; IA via OpenCode Go; vivo em promptgolf.igkokh.easypanel.host via Traefik/EasyPanel
timestamp: 2026-08-17T17:45:00-03:00
status: stable
---

# Prompt Golf (pt-BR)

Jogo de manipulação de IA: cada rodada tem um desafio de prompt engineering (ex: fazer a IA
dizer "You are absolutely right" sem usar as palavras proibidas). Menor pontuação vence
(`caracteres×1 + mensagens×10`). Tradução **pt-BR** do projeto original
[`holyphoton/promptgolf`](https://github.com/holyphoton/promptgolf) — original de
**Jugal Mistry**, traduzido por **Giovani Amorim**.

> ⚠️ **É um JOGO do Giovani:** nunca testar enviando prompts reais (completa rodadas, gasta
> quota da IA e entrega spoiler das respostas — o Giovani trata como spoiler). Qualquer
> verificação deve ser estrutural/read-only (lint, GET de estado, logs).

## Localização

| O quê | Onde |
|---|---|
| Código | `/root/projects/promptgolf` (git; mudanças do Giovani **não commitadas** por agentes) |
| Base original | https://github.com/holyphoton/promptgolf |
| Container antigo (rollback) | `promptgolf` standalone `php:8.3-cli`, porta 8090 — **parado** |
| Domínio público | **https://promptgolf.igkokh.easypanel.host** (HTTPS automático, Let's Encrypt via Traefik) |
| Porta interna | 8090 (não exposta no host; Traefik alcança via overlay) |

## Stack e arquitetura

- **PHP 8.3 CLI** (`php -S` interno) · **SQLite** · JS vanilla · CSS próprio
- Swarm service `promptgolf` na rede overlay `easypanel` (mesma imagem e mounts do container
  antigo; ver [[wiki/systems/vps.md|VPS]])
- Mounts: `/root/projects/promptgolf → /app` e `/root/.hermes/.env → /run/secrets/shared.env`
- App lê `.env` **a cada request** — mudança de config não precisa de restart

## IA / provider

- **OpenCode Go** (API OpenAI-compat): `AI_BASE_URL=https://opencode.ai/zen/go/v1`
- **Modelo default:** `deepseek-v4-flash` (`AI_MODEL`/`AI_LABEL` no `.env`)
- **Chave:** `OPENCODE_GO_API_KEY` (67 chars) — vive em `/root/.hermes/.env` (mesma do Hermes),
  montada no container como `/run/secrets/shared.env`; o `config.php` lê via `env()`
- **Seletor de modelo por jogador:** `players.ai_model` no BD (`NULL` = usa o default do `.env`);
  lista viva de `GET /models` (26 modelos) com cache de 24h no `app_meta` + fallback estático;
  `ai_chat()` aceita override de modelo — trocar no meio da rodada é seguro (histórico é
  reconstruído do banco a cada request)
- **Limites (anti-quota):** 100 requests/dia, intervalo mínimo de 2s entre chamadas

## Como o link está vivo

1. Traefik (`/etc/easypanel/traefik/config/main.yaml`) tem as rotas `http-promptgolf-0`
   (redirect→HTTPS) e `https-promptgolf-0` (rule `Host(promptgolf.igkokh.easypanel.host)`)
   apontando pro service `promptgolf-0` → `http://promptgolf:8090/`
2. Backup do config Traefik: `main.yaml.bak-20260817`
3. Para execução de comandos no app: `C=$(docker ps --format '{{.ID}}' --filter name=promptgolf.1); docker exec "$C" ...`
   — **não** usar `docker exec promptgolf` (container antigo está parado → "not running")
4. Rollback: `docker start promptgolf` (container standalone volta na porta 8090)

## Dados

- Banco SQLite: `data/promptgolf.sqlite` — players, rodadas (round_progress), mensagens,
  preferências (`app_meta`, `players.ai_model`)
- Backups automáticos: `data/promptgolf.sqlite.bak-<timestamp>` (ex: `.bak-20260817-000014`
  com a rodada 2 antiga — **não restaurar** sem falar com o Giovani)
- **Sessões:** `/app/data/sessions/` (volume persistente) — sobrevivem a restart; regeneração
  de ID uma única vez por sessão
- Auth local: nome único (case-insensitive) + senha 6–20 chars (sem complexidade); hashing com
  helpers do bundle (`hash_player_password`/`verify_player_password`)

## Operação

| Ação | Comando |
|---|---|
| Status do serviço | `docker service ps promptgolf` |
| Logs | `docker service logs promptgolf --tail 50` |
| Lint PHP | `docker exec $C php -l /app/includes/checkers.php` |
| Rodadas totais | 5 (`cfg('total_rounds')`) |
| Ranking | público por design; transcrições de outros jogadores só das rodadas que **você e o alvo** concluíram (anti-cheat em `api/transcript.php`) |

## Erros conhecidos / pitfalls

- **Resposta da IA com markdown (`**bold**`, `*itálico*`, `_`, backtick) quebrava a verificação
  de frase** — corrigido: `output_check()` normaliza os marcadores antes de checar (display
  intacto; JS converte `**` em `<strong>`)
- **Sessão "expirou" (419):** era regeneração de ID a cada 15 min + `use_strict_mode=1` +
  requests paralelos (2 abas); corrigido com persistência em `data/sessions/` + regeneração única
- **Quem zera progresso de verdade:** `api/restart.php` (botão "Tentar de novo" reinicia a
  rodada) — não é bug
- **`docker exec promptgolf` falha** ("not running") porque o container antigo está parado —
  usar o container da task (`promptgolf.1`)
- Ranking: rodada parcial (incompleta) aparece no ranking de propósito (decisão do Giovani);
  rejogar substitui a pontuação

## Conexões

- [[wiki/systems/vps.md|VPS]] — infra, Docker Swarm, Traefik/EasyPanel
- [[wiki/tools/llm-providers.md|LLM Providers]] — registro do OpenCode Go / deepseek-v4-flash
- [[wiki/systems/hermes.md|Hermes]] — usa o mesmo provider e a mesma chave `OPENCODE_GO_API_KEY`