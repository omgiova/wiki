---
type: tool
tags: [opencode-go, llm, usage, tokens, monitoramento]
title: ocg-usage — Consumo do OpenCode Go
description: CLI em Python que reporta uso detalhado do plano OpenCode Go — nº de requisições e tokens por modelo/sessão em janelas curtas (15m a 1w), que o painel da OpenCode não mostra. Uso: ocg-usage [janela].
timestamp: 2026-08-26T09:13:34-03:00
status: stable
---

# ocg-usage — Consumo do OpenCode Go

## O que é

CLI local em Python (stdlib, sem dependências) que reporta o uso do plano **OpenCode Go** com granularidade que o painel deles não oferece: o site só mostra um filtro mensal, sem nº de requisições. O `ocg-usage` entrega **nº de chamadas, tokens input/output/cache e % do plano** em janelas de 15min até 1 semana.

- **Caminho único:** `/root/scripts/ocg-usage` (script executável, `#!/usr/bin/env python3`)
- **Fonte dos dados:** tabela `session_model_usage` do `/root/.hermes/state.db` (agregado por sessão+modelo que o Hermes grava a cada chamada) + endpoint `GET https://opencode.ai/zen/go/v1/usage` para os % do plano
- **Modo de execução:** sob demanda, apenas quando invocado. Não é daemon, não é cron, não coleta nada novo — lê dados que já existem no banco.

## Capabilities

- **Janelas:** `15m` `30m` `1h` `3h` `6h` `12h` `1d` `3d` `1w` (default `1h`)
- **Por modelo:** sessões, reqs, tokens input, output, cache read/write
- **Por sessão:** top N sessões da janela (reqs, tokens, span, modelos usados, id curto, título)
- **Plano:** % rolling/weekly/monthly + horário de reset (única coisa que a API expõe)
- **Mescla por `session_id`:** linhas de task (`title_generation`, `background_review`, etc.) somam na sessão mãe — sem duplicatas na listagem
- **`--json`** para pipeline/cron; **`--top N`** para controlar a listagem de sessões

## Limites

- **Granularidade é por sessão, não por requisição individual** — o `state.db` guarda agregação por (sessão, modelo, task), não o momento de cada request. Chamadas de uma sessão são atribuídas ao `last_seen` dela (sessões longas aparecem inteiras na janela do fim dela)
- **API só dá o % do momento** — sem histórico, sem série temporal. Para acumular história é preciso cron de snapshot
- **Sem custo em USD** — `estimated_cost_usd` fica 0 (plano é assinatura ~$10/mês, não pay-per-token)
- Cobre apenas o tráfego que passa pelo Hermes (state.db) — chamadas diretas de outros apps ao OpenCode Go não aparecem

## Como usar

```bash
ocg-usage           # default 1h
ocg-usage 12h       # janela de 12h
ocg-usage 1d --top 5
ocg-usage 6h --json # saída para pipeline
```

## Quando não usar

- Quando precisar de granularidade por requisição individual (não existe fonte — nem API nem banco; só um proxy de log novo resolveria)
- Para monitoramento contínuo/histórico sem intervenção (montar cron que snapshota o rolling% num arquivo)
- Para medição de custo em reais/dólares

## Configuração

- **Nenhuma.** Lê `OPENCODE_GO_API_KEY` de `/root/.hermes/.env` e abre o `state.db` em modo somente leitura
- Instalado como arquivo único em `/root/scripts/` (padrão de organização do Giovani)

## Erros conhecidos

| Sintoma | Causa | Ação |
|---|---|---|
| `403 Forbidden` na parte do plano | Client sem User-Agent (urllib default) | Script já envia `User-Agent: curl/8.0` |
| `% plano` idêntica com qualquer parâmetro | API ignora `?period=`, `?range=`, `?days=`, `?granularity=` | Esperado — endpoint só expõe agregados |
| Sessões duplicadas na listagem | Linhas por task no banco | Resolvido — script mescla por `session_id` |

## Status de validação

- `stable` — testado ao vivo em todas as janelas (15m a 1w), 2026-08-18/26

## Conexões

- [[wiki/tools/llm-providers.md|LLM Providers]] — o provider OpenCode Go que esta ferramenta monitora
- [[wiki/systems/hermes.md|Hermes]] — grava os dados em `session_model_usage` no `state.db`