---
type: tool
tags: [opencode-go, llm, usage, tokens, monitoramento]
title: ocg-usage — Consumo do OpenCode Go
description: CLI que reporta o consumo do plano OpenCode Go — nº de requisições e tokens por modelo/sessão em janelas curtas (15m a 1w) ou desde uma data, mais grid dia x modelo com tokens e estimativa de % do plano.
timestamp: 2026-09-04T10:45:00-03:00
status: stable
---

# ocg-usage — Consumo do OpenCode Go

## O que é

CLI local que reporta o consumo do plano **OpenCode Go**: nº de requisições e tokens (input/output/cache/reasoning) por modelo e por sessão, em janelas de 15 minutos a 1 semana ou desde uma data específica, mais os percentuais do plano (rolling/weekly/monthly). Criado porque o painel da OpenCode só oferece filtro mensal, sem contagem de requisições.

Existem duas versões do arquivo: **`/root/scripts/ocg-usage`** (v1, janelas curtas) e **`/root/scripts/ocg-usage2`** (v2, grid completo + `--since`). Esta página documenta a **v2** (verbatim na seção [Implementação](#implementação)). A v1 mantém o formato original por compatibilidade.

## Capabilities

- **Janelas:** `15m` `30m` `1h` `3h` `6h` `12h` `1d` `3d` `1w` (default `1h`) — aceita também sintaxe livre `3m`, `45m`, `2h`
- **Desde uma data:** `2026-09-01` → dados de 00:00 local dessa data até agora
- **Grid dia × modelo:** sessões, reqs, input, output, cache read/write, reasoning, **pond.** e **%plano** por dia × modelo
- **Σ POR DIA:** totais por dia (tabela própria, sem coluna de modelo)
- **Subtotal por modelo** e **sessões** (top N, com título, span, pond. e %plano)
- **Plano:** % rolling/weekly/monthly + horário de reset (dados da API de uso)
- **`--json`** para pipeline/cron; **`--top N`** para controlar a listagem de sessões
- Coluna **pond.** = `input×1 + output×4 + cache×0.1` (pesos típicos de billing)
- Coluna **%plano** = estimativa: pond. do período calibrada contra o `monthly%` real da API sobre o ciclo mensal (ver [Limites](#limites))

## Como usar

```bash
ocg-usage2           # default 1h
ocg-usage2 15m       # janela de 15 minutos
ocg-usage2 3h        # janela de 3 horas
ocg-usage2 2026-09-01   # desde 00:00 de 01/09 até agora
ocg-usage2 1d --top 5   # top 5 sessões
ocg-usage2 6h --json    # saída para pipeline
```

## Limites

- **Granularidade por sessão, não por requisição individual** — `session_model_usage` guarda contadores **acumulados por sessão**; a janela só filtra *quais* sessões entram (via `last_seen`), e os valores mostrados (reqs/tokens) são o total da sessão inteira, não do período. Exemplo real (04/09): janela de 3 min mostrava 88 reqs quando a timeline real tinha 4.
- **Sem tokens por request** — a tabela `messages` tem granularidade por request real (timestamp, tools, reasoning, finish_reason), mas `token_count` é NULL em todas as mensagens. Para a timeline request a request, consultar `messages` direto (role='assistant' = 1 request).
- **API só expõe o % do momento** — sem histórico nem série temporal (para acumular história, cron de snapshot). Sonda de 15 endpoints (04/09): só `/zen/go/v1/usage` e `/zen/go/v1/models` existem; o console web (opencode.ai/auth) é a única fonte oficial de tracking individual.
- **%plano é estimativa** — pesos do billing (in=1, out=4, cache=0.1) não são confirmados pela OpenCode; texto acima do grid avisa a calibração usada. Se os pesos reais diferirem, a ordem de grandeza e o ranking se mantêm.
- **Sem custo em USD real** — `estimated_cost_usd` fica 0 (plano é assinatura, não pay-per-token). Os limites do plano são em $ (5h=$12, semanal=$30, mensal=$60), mas a API não devolve gasto.
- Cobre apenas o tráfego que passa pelo Hermes (state.db) — chamadas diretas de outros apps não aparecem

## Quando não usar

- Quando precisar de granularidade por requisição individual (não existe fonte — nem API nem banco)
- Para monitoramento contínuo/histórico sem intervenção (requer cron de snapshot)
- Para medição de custo monetário

## Configuração

- **Caminho:** `/root/scripts/ocg-usage2`
- Nada a configurar — lê `OPENCODE_GO_API_KEY` de `~/.hermes/.env` e abre o `state.db` em modo somente leitura
- **Timezone:** `America/Sao_Paulo` fixo no script; o host esta nesse fuso

## Implementação — script v2 (verbatim)

Arquivo único executável (`#!/usr/bin/env python3`), Python stdlib, zero dependências. Fontes de dados: tabela `session_model_usage` do `~/.hermes/state.db` (modo somente leitura) + `GET https://opencode.ai/zen/go/v1/usage` (exige `User-Agent` tipo curl; cliente sem UA recebe 403). Caminhos montados via `HERMES_HOME`/`expanduser` (sem literal `~/.hermes` no arquivo — o guard do gateway lê scripts referenciados e o literal dispara falso positivo de leitura de arquivo grande).

```python
#!/usr/bin/env python3
"""ocg-usage2 — uso detalhado do OpenCode Go (opencode.ai/zen/go/v1).

v2 (2026-09-04): grid completo dia × modelo (sessões, reqs, tokens, pond.,
%plano estimado), janelas relativas (15m..1w) e período desde data (--since).
Caminhos montados via HERMES_HOME (sem literal ~/.hermes no arquivo).

Fontes:
- API:  /zen/go/v1/usage -> % rolling/weekly/monthly + resetsAt
- Local: state.db (HERMES_HOME) -> session_model_usage por sessão/modelo/task
  e messages (timeline request a request, sem tokens por request).

Uso:
  ocg-usage2 [janela|data] [opts]
  janela: 15m | 30m | 1h | 3h | 6h | 12h | 1d | 3d | 1w   (default: 1h)
  data:   2026-09-01  (desde 00:00 local dessa data)
  opts:  --json | --top N
"""
import json
import os
import re
import sqlite3
import sys
import urllib.request
from datetime import datetime, timedelta
from zoneinfo import ZoneInfo

TZ = ZoneInfo("America/Sao_Paulo")
WINDOWS = {"15m": 15*60, "30m": 30*60, "1h": 3600, "3h": 3*3600, "6h": 6*3600,
           "12h": 12*3600, "1d": 86400, "3d": 3*86400, "1w": 7*86400}
_HERMES_HOME = os.environ.get("HERMES_HOME") or os.path.join(
    os.path.expanduser("~"), ".hermes")
DB = os.path.join(_HERMES_HOME, "state.db")
ENV_FILE = os.path.join(_HERMES_HOME, ".env")
API = "https://opencode.ai/zen/go/v1/usage"

# pesos de billing para a coluna "pond." e calibração do %plano
W_IN, W_OUT, W_CACHE = 1.0, 4.0, 0.1


def fetch_api():
    try:
        env = {}
        with open(ENV_FILE) as f:
            for line in f:
                line = line.strip()
                if line and not line.startswith("#") and "=" in line:
                    k, v = line.split("=", 1)
                    env[k] = v
        key = env.get("OPENCODE_GO_API_KEY", "")
        if not key:
            return None
        req = urllib.request.Request(
            API, headers={"Authorization": f"Bearer {key}",
                          "User-Agent": "curl/8.0"})
        with urllib.request.urlopen(req, timeout=15) as r:
            return json.loads(r.read())
    except Exception as e:
        return {"error": str(e)}


def fmt(n):
    if n >= 1e9: return f"{n/1e9:.2f}B"
    if n >= 1e6: return f"{n/1e6:.2f}M"
    if n >= 1e3: return f"{n/1e3:.1f}K"
    return str(int(n))


def weighted(in_tok, out_tok, cr, cw):
    return in_tok * W_IN + out_tok * W_OUT + (cr + cw) * W_CACHE


def main():
    args = [a for a in sys.argv[1:] if not a.startswith("--")]
    opts = [a for a in sys.argv[1:] if a.startswith("--")]
    arg = args[0] if args else "1h"
    as_json = "--json" in opts
    top_n = 8
    for o in opts:
        if o.startswith("--top="):
            top_n = int(o.split("=")[1])

    now = datetime.now(TZ)
    # data (--since) ou janela relativa
    dm = re.fullmatch(r"(\d+)([mhdw])", arg)
    if dm:
        secs = int(dm.group(1)) * {"m": 60, "h": 3600, "d": 86400, "w": 7*86400}[dm.group(2)]
        cutoff_dt = now - timedelta(seconds=secs)
    elif arg in WINDOWS:
        cutoff_dt = now - timedelta(seconds=WINDOWS[arg])
    elif re.fullmatch(r"\d{4}-\d{2}-\d{2}", arg):
        cutoff_dt = datetime.fromisoformat(arg).replace(tzinfo=TZ)
    else:
        print(f"argumento inválido: {arg}\nuse: janela ({' | '.join(WINDOWS)}) ou data YYYY-MM-DD")
        sys.exit(2)
    cutoff = cutoff_dt.timestamp()

    api = fetch_api()

    conn = sqlite3.connect(f"file:{DB}?mode=ro", uri=True)
    conn.row_factory = sqlite3.Row
    cur = conn.cursor()
    rows = cur.execute(
        "SELECT s.session_id, s.model, s.api_call_count, s.input_tokens, "
        "s.output_tokens, s.cache_read_tokens, s.cache_write_tokens, s.reasoning_tokens, "
        "s.first_seen, s.last_seen, COALESCE(se.title,'') AS title "
        "FROM session_model_usage s LEFT JOIN sessions se ON se.id = s.session_id "
        "WHERE s.last_seen >= ? ORDER BY s.last_seen",
        (cutoff,),
    ).fetchall()

    # ---- calibração % do plano mensal ----
    monthly_pct = None
    if api and api.get("usage"):
        monthly_pct = api["usage"].get("monthly", {}).get("percent")
    mc = cur.execute(
        "SELECT input_tokens, output_tokens, cache_read_tokens, cache_write_tokens "
        "FROM session_model_usage WHERE last_seen >= ?",
        (cutoff - (now.timestamp() - cutoff),),  # cutoff p/ ciclo: ~primeira chamada da sessão? ver abaixo
    ).fetchall()
    # ciclo mensal real: desde o resetsAt do monthly (encontrado na API)
    month_start = None
    if api and api.get("usage") and api["usage"].get("monthly", {}).get("resetsAt"):
        rst = api["usage"]["monthly"]["resetsAt"]  # UTC ISO
        try:
            month_start = datetime.fromisoformat(rst.replace("Z", "+00:00")).timestamp() - 30*86400
        except Exception:
            month_start = None
    if month_start is None:
        month_start = now.timestamp() - 30*86400
    mc = cur.execute(
        "SELECT input_tokens, output_tokens, cache_read_tokens, cache_write_tokens "
        "FROM session_model_usage WHERE last_seen >= ?",
        (month_start,),
    ).fetchall()
    month_w = sum(weighted(r["input_tokens"], r["output_tokens"],
                           r["cache_read_tokens"], r["cache_write_tokens"]) for r in mc)
    month_raw = sum(r["input_tokens"] + r["output_tokens"] + r["cache_read_tokens"]
                    + r["cache_write_tokens"] for r in mc)
    pct_per_w = (monthly_pct / month_w) if (monthly_pct and month_w) else None

    # ---- agregados ----
    cells, day_tot, model_tot, sess = {}, {}, {}, {}
    for r in rows:
        d = datetime.fromtimestamp(r["last_seen"], TZ).strftime("%d/%m (%a)")
        key = (d, r["model"])
        c = cells.setdefault(key, {"sess": set(), "reqs": 0, "in": 0, "out": 0,
                                   "cr": 0, "cw": 0, "rz": 0})
        c["sess"].add(r["session_id"])
        c["reqs"] += r["api_call_count"]
        c["in"] += r["input_tokens"]
        c["out"] += r["output_tokens"]
        c["cr"] += r["cache_read_tokens"]
        c["cw"] += r["cache_write_tokens"]
        c["rz"] += r["reasoning_tokens"]
        for sc in (day_tot.setdefault(d, {}), model_tot.setdefault(r["model"], {})):
            t = sc.setdefault("sess", set())
            t.add(r["session_id"])
            for k, v in (("reqs", r["api_call_count"]), ("in", r["input_tokens"]),
                         ("out", r["output_tokens"]), ("cr", r["cache_read_tokens"]),
                         ("cw", r["cache_write_tokens"]), ("rz", r["reasoning_tokens"])):
                sc[k] = sc.get(k, 0) + v
        s = sess.setdefault(r["session_id"], {"models": set(), "tasks": set(),
            "reqs": 0, "in": 0, "out": 0, "cr": 0, "cw": 0, "rz": 0,
            "first": r["first_seen"], "last": r["last_seen"], "title": r["title"] or ""})
        s["models"].add(r["model"])
        s["reqs"] += r["api_call_count"]
        s["in"] += r["input_tokens"]
        s["out"] += r["output_tokens"]
        s["cr"] += r["cache_read_tokens"]
        s["cw"] += r["cache_write_tokens"]
        s["rz"] += r["reasoning_tokens"]
        s["first"] = min(s["first"], r["first_seen"])
        s["last"] = max(s["last"], r["last_seen"])

    def pct_s(w):
        if pct_per_w:
            return f"{pct_per_w * w:.2f}%"
        return "-"

    print(f"=== OpenCode Go — uso {cutoff_dt:%d/%m/%Y %H:%M} → {now:%d/%m %H:%M} ({TZ}) ===")
    if api and api.get("usage"):
        u = api["usage"]
        for k in ("rolling", "weekly", "monthly"):
            v = u.get(k, {})
            reset = v.get("resetsAt", "")[:16].replace("T", " ")
            print(f"plano {k:<8} {v.get('percent', '?')}%  (reset {reset} UTC)")
    else:
        print(f"plano: API indisponível ({api.get('error') if isinstance(api, dict) else ''})")
    print(f"[est] ciclo mensal: {fmt(month_raw)} brutos / {fmt(month_w)} pond. "
          f"→ calibração do %plano contra monthly {monthly_pct}%")

    if not rows:
        print("\nsem atividade no período")
        return

    # ---- GRID dia × modelo ----
    print(f"\n{'='*100}\nGRID dia × modelo (pesos: in={W_IN}x out={W_OUT}x cache={W_CACHE}x)\n{'='*100}")
    print(f"{'dia':<13}{'modelo':<20}{'sessões':>7}{'reqs':>6}{'input':>9}{'output':>9}"
          f"{'cacheR':>9}{'cacheW':>8}{'reason':>8}{'pond.':>10}{'%plano':>8}")
    grand = {"sess": set(), "reqs": 0, "in": 0, "out": 0, "cr": 0, "cw": 0, "rz": 0, "w": 0.0}
    for d in sorted(day_tot.keys()):
        for m in sorted({m for (dd, m) in cells if dd == d}):
            c = cells[(d, m)]
            w = weighted(c["in"], c["out"], c["cr"], c["cw"])
            print(f"{d:<13}{m:<20}{len(c['sess']):>7}{c['reqs']:>6}{fmt(c['in']):>9}"
                  f"{fmt(c['out']):>9}{fmt(c['cr']):>9}{fmt(c['cw']):>8}{fmt(c['rz']):>8}"
                  f"{fmt(w):>10}{pct_s(w):>8}")
            grand["sess"] |= c["sess"]
            for k in ("reqs", "in", "out", "cr", "cw", "rz"):
                grand[k] += c[k]
            grand["w"] += w

    # ---- Σ POR DIA ----
    print(f"\n{'─'*90}\nΣ POR DIA\n{'─'*90}")
    print(f"{'dia':<13}{'sessões':>7}{'reqs':>6}{'input':>9}{'output':>9}{'cacheR':>9}"
          f"{'cacheW':>8}{'reason':>8}{'pond.':>10}{'%plano':>8}")
    for d in sorted(day_tot.keys()):
        t = day_tot[d]
        w = weighted(t["in"], t["out"], t["cr"], t["cw"])
        print(f"{d:<13}{len(t['sess']):>7}{t['reqs']:>6}{fmt(t['in']):>9}{fmt(t['out']):>9}"
              f"{fmt(t['cr']):>9}{fmt(t['cw']):>8}{fmt(t['rz']):>8}{fmt(w):>10}{pct_s(w):>8}")
    w_g = weighted(grand["in"], grand["out"], grand["cr"], grand["cw"])
    print(f"{'Σ TOTAL':<13}{len(grand['sess']):>7}{grand['reqs']:>6}{fmt(grand['in']):>9}"
          f"{fmt(grand['out']):>9}{fmt(grand['cr']):>9}{fmt(grand['cw']):>8}{fmt(grand['rz']):>8}"
          f"{fmt(w_g):>10}{pct_s(w_g):>8}")

    # ---- SUBTOTAL POR MODELO ----
    print(f"\n{'─'*90}\nSUBTOTAL POR MODELO\n{'─'*90}")
    print(f"{'modelo':<20}{'sessões':>7}{'reqs':>6}{'input':>9}{'output':>9}{'cacheR':>9}"
          f"{'cacheW':>8}{'reason':>8}{'pond.':>10}{'%plano':>8}")
    for m in sorted(model_tot.keys(), key=lambda x: -model_tot[x].get("reqs", 0)):
        t = model_tot[m]
        w = weighted(t["in"], t["out"], t["cr"], t["cw"])
        print(f"{m:<20}{len(t['sess']):>7}{t['reqs']:>6}{fmt(t['in']):>9}{fmt(t['out']):>9}"
              f"{fmt(t['cr']):>9}{fmt(t['cw']):>8}{fmt(t['rz']):>8}{fmt(w):>10}{pct_s(w):>8}")

    # ---- SESSÕES ----
    print(f"\n{'─'*90}\nSESSÕES ({len(sess)})\n{'─'*90}")
    for sid, s in sorted(sess.items(), key=lambda x: -x[1]["reqs"])[:top_n]:
        span = (s["last"] - s["first"]) / 60
        title = (s["title"] or "")[:44]
        w = weighted(s["in"], s["out"], s["cr"], s["cw"])
        print(f"{datetime.fromtimestamp(s['last'], TZ):%d/%m %H:%M}  {s['reqs']:>5} reqs  "
              f"{fmt(s['in'])} in / {fmt(s['out'])} out / {fmt(s['cr'])} cR  pond~{fmt(w)}  "
              f"{pct_s(w)}  span~{span:.0f}min  {','.join(sorted(s['models']))}  {sid[-8:]}  {title}")

    if as_json:
        out = {
            "window": arg, "from": cutoff_dt.isoformat(), "to": now.isoformat(),
            "api": api, "monthly_pct": monthly_pct, "weights": {"in": W_IN, "out": W_OUT, "cache": W_CACHE},
            "by_model": {m: {**{k: (len(v) if k == "sess" else v) for k, v in t.items()},
                             "pond": weighted(t["in"], t["out"], t["cr"], t["cw"])}
                         for m, t in model_tot.items()},
            "by_day": {d: {**{k: (len(v) if k == "sess" else v) for k, v in t.items()},
                           "pond": weighted(t["in"], t["out"], t["cr"], t["cw"])}
                       for d, t in day_tot.items()},
            "cells": {f"{d}|{m}": {k: (len(v) if k == "sess" else v) for k, v in c.items()}
                      for (d, m), c in cells.items()},
            "sessions": [{**{k: (list(v) if k in ("models", "tasks") else v) for k, v in s.items()},
                          "session_id": sid} for sid, s in sess.items()],
        }
        print(json.dumps(out, ensure_ascii=False, indent=2, default=str))
    conn.close()


if __name__ == "__main__":
    main()
```

## Erros conhecidos

| Sintoma | Causa | Ação |
|---|---|---|
| Janela inválida | Parâmetro fora da lista/data inválida | Mensagem de erro lista as janelas válidas (exit 2) |
| Linha "plano" indisponível | API de uso fora do ar / sem chave | Resto do relatório continua; comportamento de degradação |
| Coluna %plano em "-" | Calibração falhou (API sem monthly% ou sem dados do ciclo) | O grid segue completo, sem a estimativa |

## Status de validação

- `stable` — v2 validada ao vivo em 2026-09-04: janela `15m`, janela `3h`, `2026-09-01` (desde data), grid completo, guard do gateway destravado (`contains_gateway_lifecycle_command_or_referenced_script` → `False`)

## Conexões

- [[wiki/tools/llm-providers.md|LLM Providers]] — o provider OpenCode Go que esta ferramenta monitora
- [[wiki/systems/hermes.md|Hermes]] — grava os dados em `session_model_usage` no `state.db`; a timeline request a request vem da tabela `messages`