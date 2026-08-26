---
type: tool
tags: [opencode-go, llm, usage, tokens, monitoramento]
title: ocg-usage — Consumo do OpenCode Go
description: CLI que reporta o consumo do plano OpenCode Go — nº de requisições e tokens por modelo/sessão em janelas curtas (15m a 1w), que o painel da OpenCode não mostra.
timestamp: 2026-08-26T09:13:34-03:00
status: stable
---

# ocg-usage — Consumo do OpenCode Go

## O que é

CLI local que reporta o consumo do plano **OpenCode Go**: nº de requisições e tokens (input/output/cache) por modelo e por sessão, em janelas de 15 minutos a 1 semana, mais os percentuais do plano. Criado porque o painel da OpenCode só oferece filtro mensal, sem contagem de requisições.

## Capabilities

- **Janelas:** `15m` `30m` `1h` `3h` `6h` `12h` `1d` `3d` `1w` (default `1h`)
- **Por modelo:** sessões, requisições, tokens input, output, cache read/write
- **Por sessão:** top N sessões da janela (requisições, tokens, span, modelos usados, título)
- **Plano:** % rolling/weekly/monthly + horário de reset (dados da API de uso)
- **Listagem de sessões sem duplicatas**
- **`--json`** para pipeline/cron; **`--top N`** para controlar a listagem

## Como usar

```bash
ocg-usage           # default 1h
ocg-usage 12h       # janela de 12h
ocg-usage 1d --top 5
ocg-usage 6h --json # saída para pipeline
```

## Limites

- **Granularidade por sessão, não por requisição individual** — chamadas de uma sessão são atribuídas ao `last_seen` dela (sessões longas aparecem inteiras na janela do fim dela)
- **API só expõe o % do momento** — sem histórico nem série temporal (para acumular história, cron de snapshot)
- **Sem custo em USD** — `estimated_cost_usd` fica 0 (plano é assinatura, não pay-per-token)
- Cobre apenas o tráfego que passa pelo Hermes (state.db) — chamadas diretas de outros apps não aparecem

## Quando não usar

- Quando precisar de granularidade por requisição individual (não existe fonte — nem API nem banco)
- Para monitoramento contínuo/histórico sem intervenção (requer cron de snapshot)
- Para medição de custo monetário

## Configuração

- **Caminho:** `/root/scripts/ocg-usage`
- Nada a configurar — lê `OPENCODE_GO_API_KEY` de `~/.hermes/.env` e abre o `state.db` em modo somente leitura

## Implementação — construção do script

### 1. Arquivo único executável (shebang)
`/root/scripts/ocg-usage` começa com `#!/usr/bin/env python3` — é isso que permite executar o arquivo direto (`/root/scripts/ocg-usage`) sem prefixar `python3`, em qualquer ambiente com Python 3 no PATH. Se o arquivo for copiado sem o bit de execução ou o shebang for alterado, o comando para de rodar direto.
Verificar: `head -1 /root/scripts/ocg-usage`; `ls -l` deve mostrar `-rwxr-xr-x`.

### 2. Python stdlib, zero dependências externas
Só bibliotecas padrão (`json`, `sqlite3`, `urllib`, `os`, `sys`, `datetime`) — nenhum `pip install`, nenhum requisito de runtime além de Python 3.x. Portável: pode ser copiado para outra máquina sem setup.

### 3. Mescla por `session_id` (por que não há duplicatas)
O `state.db` guarda uma linha por (sessão, modelo, provider, task). Tasks internas do Hermes (`title_generation`, `background_review`, ...) geram linhas extras de 1 chamada para a mesma sessão — sem tratamento, a listagem mostraria a mesma sessão várias vezes. O script soma todas as linhas da mesma `session_id` na sessão mãe. Consequência estrutural: agregados são atribuídos ao `last_seen` da sessão (o banco não guarda o momento de cada request).

### 4. Fontes de dados (banco + API)
- **Banco:** tabela `session_model_usage` do `~/.hermes/state.db`, aberta em modo somente leitura — registros com `api_call_count`, tokens in/out/cache/reasoning, `first_seen`/`last_seen`; gravados pelo Hermes a cada resposta.
- **API do plano:** `GET https://opencode.ai/zen/go/v1/usage` com Bearer `OPENCODE_GO_API_KEY` — devolve só % rolling/weekly/monthly + `resetsAt`; ignora qualquer parâmetro de filtro; exige `User-Agent` tipo curl (cliente sem UA recebe 403).

## Erros conhecidos

| Sintoma | Causa | Ação |
|---|---|---|
| Janela inválida | Parâmetro fora da lista | Mensagem de erro lista as janelas válidas (exit 2) |
| Linha "plano" indisponível | API de uso fora do ar | Resto do relatório continua; comportamento de degradação |

## Status de validação

- `stable` — todas as janelas validadas ao vivo (2026-08-26)

## Conexões

- [[wiki/tools/llm-providers.md|LLM Providers]] — o provider OpenCode Go que esta ferramenta monitora
- [[wiki/systems/hermes.md|Hermes]] — grava os dados em `session_model_usage` no `state.db`