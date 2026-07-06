---
type: system
tags: [hermes, config, identity, rules, modelos, mcp]
title: Hermes Agent
description: Sistema principal do Giovani — identidade, stack, modelos, interface REST e operação do Hermes Agent
timestamp: 2026-07-02T00:00:00-03:00
status: stable
---

# Hermes Agent

## O que é

Assistente pessoal do Giovani. Direto, técnico, eficiente.

**Estilo de resposta:**
- Respostas curtas (máx 10 linhas)
- PT-BR sempre
- Ação > explicação
- Sem intro/frescura

**Preferências técnicas:**
- TypeScript/JavaScript > Python para apps
- Python para scripts/automação
- Preferir Docker para deploy
- Testar antes de assumir que funciona

## Stack e configuração

- **VPS:** Hostinger KVM 2 (Ubuntu) — ver [[wiki/systems/vps.md|vps]]
- **Automação:** [[wiki/systems/n8n.md|n8n]] + Node-RED
- **Mídia:** React + Remotion
- **Gateway:** Telegram (celular)

**Componentes:**

| Arquivo/Pasta | Papel |
|---|---|
| `SOUL.md` | System prompt fixo do Hermes |
| `config.yaml` | Configurações do Hermes |
| `plugins/` | Plugins habilitados (spotify, dashboard_auth) |
| `skills/` | Skills instaladas |
| `cron/` | Jobs agendados |

**Modelos (2026-06-22):**

- **Principal:** `moonshotai/kimi-k2.6` via Nvidia NIM
- **Vision / auxiliary.vision:** `minimaxai/minimax-m3` via Nvidia NIM (suporta vídeo até 30min)
- **Provedor:** Nvidia NIM (`https://integrate.api.nvidia.com/v1`)
- **Fallback:** não configurado

13 slots auxiliares fixos (`auxiliary.*` no config.yaml). Cada slot aceita `fallback_chain` próprio e independente. NIM free tier funciona bem em slots auxiliares (1 chamada por ativação), mas não como modelo principal agêntico (15–20 chamadas/tarefa → 429).

**APIs configuradas no `.env`:**

- `NVIDIA_API_KEY` — Nvidia NIM (modelos principais e auxiliares)
- `GOOGLE_API_KEY` — Gemini
- `ELEVENLABS_API_KEY` — ElevenLabs TTS/STT
- `GROQ_API_KEY` — Groq (STT whisper; LLM pendente fix de compatibilidade)

**Web Search:**

- Backend: Firecrawl (`search_backend: firecrawl`, `extract_backend: firecrawl`)
- API key e URL configurados no `.env` (`FIRECRAWL_API_KEY`, `FIRECRAWL_API_URL`)
- Usar `web_search` para buscas rápidas, `firecrawl search` via terminal para buscas avançadas

**MCP Servers:**

O Hermes registra MCPs no bloco `mcp_servers:` do `config.yaml` (mudanças só valem após restart do gateway). A lista completa dos MCPs da VPS, com links e status de registro por agente, fica no [[wiki/concepts/mcps.md|Registro central de MCPs]].

## Interface

REST API disponível em `http://localhost:9119`. Docs interativas: `http://localhost:9119/docs`.

**Auth:** POST `/auth/password-login` com `{"username":"omgiova","password":"15071995","provider":"basic"}` → cookie `hermes_session_at`

| Campo | Valor |
|---|---|
| URL dashboard | `http://2.24.121.135:9119` |
| Username | `omgiova` |
| Password | `15071995` |
| Password hash | scrypt — gerado via `plugins.dashboard_auth.basic.hash_password('15071995')` |

Referência completa dos ~180 endpoints REST agrupados por domínio: [[wiki/systems/hermes-endpoints.md|Hermes API]].

## Operação

**Regras de comportamento do agente:**

- **Sempre carregar a skill relevante** (`skill_view(nome)`) antes de qualquer ação
- **Se a skill descreve o comando, usar exatamente como está** — não adaptar, não testar alternativas
- **Só investigar se o comando da skill falhar** — partindo da skill como fonte da verdade
- **Nunca modificar uma skill sem autorização explícita** do usuário

> Origem: sessão 2026-06-18 — agente ignorou skill `alexa-notifications` e desperdiçou dezenas de comandos desnecessários.

**Skills — Bundled vs User:**

| Tipo | Como identificar | Pode editar? |
|---|---|---|
| Bundled | `git -C /root/.hermes ls-files skills/<path>` retorna o path | **NÃO** — causa conflito no `/update` |
| User | `git -C /root/.hermes ls-files skills/<path>` retorna vazio | Sim |

Sempre verificar com `git ls-files` antes de editar qualquer arquivo em `skills/`. Editar uma skill bundled causa `M` no git status → stash conflict no `/update` → `git reset --hard HEAD` apagando dados do usuário.

**Proteção permanente:** `/root/.hermes/.git/info/exclude` exclui arquivos críticos de runtime do stash. Local — nunca commitado, nunca sobrescrito por `hermes update`.

## Erros conhecidos

### Dashboard auth — 4 pitfalls

Configuração do plugin `dashboard_auth/basic` no `config.yaml`:

```yaml
plugins:
  enabled:
    - dashboard_auth/basic
  dashboard_auth:
    basic:
      username: omgiova
      password_hash: <hash scrypt>
```

1. **Campo é `password_hash`, não `password`** — gateway recusa bind com erro "Refusing to bind dashboard to 0.0.0.0 — no auth providers registered" se o hash estiver vazio ou o campo errado for usado
2. **Plugin `dashboard_auth/basic` não carrega automaticamente** — precisa estar declarado em `plugins.enabled` (opt-in); sem isso, nenhuma autenticação é registrada
3. **`hermes-dashboard.service` não pode ser reiniciado de dentro do gateway** — erro "cannot restart or stop the gateway from inside the gateway process"; reinício deve ser feito via SSH direto no VPS: `systemctl restart hermes-dashboard.service`
4. **`hermes config set` corrompe arrays** — serializa como string YAML com escapes (`"[\\n  spotify\\n  dashboard_auth/basic\\n]"`); sempre corrigir manualmente via Python para formato YAML válido após usar `hermes config set` em campos de array

## Conexões

- [[wiki/systems/vps.md|VPS]] — hardware e serviços da stack
- [[wiki/systems/n8n.md|n8n]] — plataforma de automação consumida via MCP
- [[wiki/systems/hermes-endpoints.md|Hermes API]] — referência completa dos endpoints REST
- [[wiki/concepts/wiki.md|Wiki]] — base de conhecimento
- [[wiki/concepts/mcps.md|Registro central de MCPs]] — lista oficial dos MCPs da VPS e onde cada um está registrado
- [[wiki/tools/firecrawl.md|Firecrawl]] — busca multi-plataforma
- [[wiki/tools/elevenlabs-mcp.md|ElevenLabs MCP]] — síntese de voz via MCP
- [[wiki/history/crise-update.md|Crise update]] — recuperação de sessões
- [[wiki/todo/proximos-passos.md|Próximos passos]]
