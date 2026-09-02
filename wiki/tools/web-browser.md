---
type: tool
tags: [web, browser, firecrawl, hermes, scraping, search, browser-use]
title: Web & Browser — acesso a sites, busca e scraping no setup
description: Catálogo único das ferramentas de acesso web do setup (busca, extração, browser automation), com estados atuais, requisitos e troubleshooting. Ferramentas são voláteis — esta página é o ponto de verdade; detalhes de implementação mudam com o tempo.
timestamp: 2026-09-02T14:00:00-03:00
status: stable
---

# Web & Browser — acesso a sites, busca e scraping

Página central sobre **tudo que acessa a internet para ler/raspar/navegar**: busca (`web_search`), extração (`web_extract`), browser automation (`browser_exec` / Browser Use CLI 3.0), o CLI `firecrawl` e o helper `ler()`.

> **Ferramentas são voláteis.** O que funciona hoje (Firecrawl, Browser Use, etc.) pode ser substituído amanhã. Esta página descreve o *capability* (um LLM acessar a web) e o estado **verificado ao vivo** de cada caminho — não fixe nomes de binário como verdades eternas. Ao migrar de ferramenta, mantenha esta página como o mapa.

## Caminhos disponíveis (estado verificado 2026-09-02)

| Caminho | O que faz | Estado | Como invocar |
|---|---|---|---|
| `web_search` (nativo Hermes) | busca indexada, retorna URLs/títulos/snippets | ✅ funciona | tool `web_search` |
| `web_extract` (nativo Hermes) | extrai conteúdo de URL(s) | ✅ funciona | tool `web_extract` |
| `firecrawl` CLI | busca/scrape/map/crawl via Firecrawl (Node) | ✅ funciona | `firecrawl search/scrape/map/crawl` no terminal |
| `ler(url)` | helper Python que chama Firecrawl v2 API direto (urllib) | ✅ funciona | `ler("https://...")` no browser_exec ou script |
| `curl` / egress HTTP | HTTP cru do terminal | ✅ funciona | `curl` |
| `browser_exec` | navegação/interação real em browser (Browser Use CLI 3.0) | ✅ funciona (com Chrome de pé) | tool `browser_exec` |

## web_search / web_extract (nativos do Hermes)

- Backend configurado em `config.yaml`: `web.search_backend` e `web.extract_backend` (ex: `firecrawl`).
- **Requisito crítico:** os web plugins são **opt-in**. Precisam estar na lista `plugins.enabled` do config, senão o registry fica vazio e as tools nativas erram com *"no registered web search provider"*. Habilitar via `hermes plugins enable web/<vendor>`.
- Backends suportados (todos em `plugins/web/`): `firecrawl` (default), `ddgs` (DuckDuckGo, free), `brave-free`, `searxng`, `exa`, `parallel`, `tavily`, `keenable`, `xai`. Busca e extract podem usar backends diferentes.
- Tier keyless: instalação sem credencial nenhuma ainda roda via anel Exa/Parallel/Firecrawl/Keenable (round-robin). Desligar com `web.keyless_fallback: false`.

## firecrawl CLI

Binário Node (`#!/usr/bin/env node`), independente do Python do Hermes. Usado quando as tools nativas não estão disponíveis ou pra controle fino.

```bash
# Busca em plataforma específica (sintaxe site:)
firecrawl search "site:github.com <query>" --scrape --limit 3
firecrawl search "site:reddit.com <query>" --scrape --limit 3
firecrawl search "site:x.com OR site:twitter.com <query>" --scrape --limit 3

# Extrair conteúdo de uma URL
firecrawl scrape "https://exemplo.com"

# Mapear um site / crawling em massa
firecrawl map "https://docs.exemplo.com" --search "installation"
firecrawl crawl "https://docs.exemplo.com" --limit 50
```

- Sempre usar `--scrape` para ver o conteúdo completo, não só o snippet.
- Consome créditos da conta Firecrawl (free tier: 500/mês) — não usar para páginas simples quando há alternativa.
- Troubleshooting: 0 resultados → simplificar query; HTML vazio em `--scrape` → SPA com JS pesado (ver snippet sem `--scrape`); timeout → reduzir `--limit`.

## ler(url) — helper Python

Função que chama a Firecrawl v2 API direto via `urllib` (não depende do plugin Python do Hermes). Útil quando `web_extract` nativo está indisponível.

```python
import os, json, urllib.request
VAR = "FIRECRAWL_API" + "_KEY"
key = next(l.split("=",1)[1].strip() for l in open("/root/.hermes/.env")
           if l.startswith(VAR + "=") and len(l.strip()) > len(VAR) + 3)
req = urllib.request.Request(
    "https://api.firecrawl.dev/v2/scrape",
    data=json.dumps({"url": url, "formats": ["markdown"], "onlyMainContent": True}).encode(),
    headers={"Authorization": "Bearer " + key, "Content-Type": "application/json"})
d = json.loads(urllib.request.urlopen(req, timeout=180).read())
print(d["data"]["markdown"])
```

## browser_exec — Browser Use CLI 3.0

Navegação e interação real em browser. O Hermes usa o **Browser Use CLI 3.0** (binário oficial `browser-harness`) como driver padrão quando `browser.backend` está unset e o CLI é runnable.

### Requisito obrigatório: Chrome/Chromium com remote debugging acessível (CDP)

- **Local Chrome não precisa de conta** (Browser Use Cloud é opcional/pago).
- O harness conecta via **CDP** a um Chrome que já esteja rodando com remote debugging. No Linux o Hermes procura em `/usr/bin/chromium-browser`, `/usr/bin/chromium`, `/usr/bin/google-chrome*`, Brave, Edge.
- Chrome snap (`/usr/bin/chromium-browser` → `/snap/bin/chromium`) **costuma travar o daemon** do harness (erro `daemon alive FAIL: CDP endpoint not reachable`). Prefira Chrome de sistema não-snap.
- Ligar remote debugging no Chrome desktop: `chrome://inspect/#remote-debugging` → tick "Allow remote debugging for this browser instance".
- O harness respeita `BU_CDP_URL` (ex: `http://127.0.0.1:9222`) apontando pro Chrome de pé.

### Instalação oficial (Browser Use CLI 3.0 / browser-harness)

```bash
uv tool install --python 3.12 --upgrade --force browser-harness
```

O binário `browser-harness` é o comando oficial do CLI 3.0. Validar com `browser-harness --doctor` (deve mostrar `chrome running [ok]` e `daemon alive [ok]`).

### Troubleshooting (oficial `browser-harness --doctor`)

- `chrome running` FAIL → abrir Chrome ou usar cloud/isolated.
- `daemon alive` FAIL → permissão de remote debugging faltando, Chrome fechado, ou CDP não alcançável (caso típico do snap).
- update disponível → `browser-harness --update -y`.

### Exemplo de uso (browser_exec)

```python
goto_url("https://example.com")
wait_for_load()
print(page_info())
```

## Conexões

- [[wiki/systems/hermes.md|Hermes]] — onde web/browser estão configurados na stack
- [[wiki/systems/vps.md|VPS]] — onde o Chromium/binários vivem
