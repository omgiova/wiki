---
type: tool
tags: [llm, usage, tokens, monitoramento, opencode-go, providers]
title: llm-usage — Consumo de LLMs registrados no Hermes
description: CLI que reporta consumo de LLMs (todos os providers) por reqs/tokens/modelo/sessão em janelas curtas ou desde uma data, grid dia x provider x modelo com pond. e % do plano OpenCode Go.
timestamp: 2026-09-04T12:30:00-03:00
status: stable
---

# llm-usage — Consumo de LLMs registrados no Hermes

## O que é

CLI local que reporta o consumo de **todos os LLMs** que passaram pelo Hermes: nº de requisições e tokens (input/output/cache/reasoning) por dia × provider × modelo, em janelas de minutos a semanas ou desde uma data específica, mais os percentuais do plano **OpenCode Go** (rolling/weekly/monthly). Sucessor direto do `ocg-usage` (v1) e `ocg-usage2` (v2): agora com discriminação por **provider** (`billing_provider` do state.db), porque o banco guarda tráfego de vários providers (opencode-go, deepseek, openrouter, anthropic, nvidia, nous, gemini, ...).

**Arquivos:**
- **`/root/scripts/llm-usage`** — script atual (v2 oficial, documentado nesta página, verbatim na seção [Implementação](#implementação))
- `/root/scripts/ocg-usage` — v1 antiga, mantida intacta por compatibilidade (bloqueada pelo guard do gateway — ver [Erros conhecidos](#erros-conhecidos))

## Capabilities

- **Janelas relativas:** `15m` `30m` `1h` `3h` `6h` `12h` `1d` `3d` `1w` (default `1h`) — aceita sintaxe livre `3m`, `45m`, `2h` (qualquer `N` + `m/h/d/w`)
- **Desde uma data:** `2026-09-01` → dados de 00:00 local dessa data até agora (00:00 em `America/Sao_Paulo`)
- **GRID dia × provider × modelo:** sessões, reqs, input, output, cache read/write, reasoning, **pond.** e **%plano** — único por (dia, provider, modelo)
- **Σ POR DIA:** totais por dia (tabela própria, sem coluna de modelo)
- **Σ POR PROVIDER:** todos os modelos de cada provider somados
- **Subtotal por provider / modelo** e **sessões** (top N, com título, span, pond. e %plano)
- **`--md`**: emite as tabelas em markdown pronto para colar **cru** no Telegram (uso do agente; rich message recebe até 32.768 chars, então janelas grandes chegam inteiras). **Nunca em bloco de código — ver regra obrigatória em [Entrega no Telegram](#entrega-no-telegram--regra-obrigatória)**
- **`--json`** para pipeline/cron; **`--top N`** para controlar a listagem de sessões
- Coluna **pond.** = `input×1 + output×4 + cache×0.1` (pesos típicos de billing; exibidos no cabeçalho do grid)
- Coluna **%plano** = estimativa: pond. do período calibrada contra o `monthly%` real da API, usando **só tráfego do provider do plano** (`opencode-go`); linha de outro provider → `-`

## Como usar

```bash
llm-usage                 # default 1h
llm-usage 15m             # últimos 15 minutos
llm-usage 3h              # últimos 3 horas
llm-usage 7d              # últimos 7 dias
llm-usage 1w              # última semana
llm-usage 30d             # últimos 30 dias
llm-usage 2026-09-01      # desde 00:00 de 01/09 até agora
llm-usage 7d --md         # tabelas markdown prontas (Telegram)
llm-usage 30d --md        # janela grande: rich recebe inteira numa mensagem
llm-usage 1d --top 5      # top 5 sessões do dia
llm-usage 6h --json       # saída estruturada para pipeline
```

**Nota de tamanho de saída:** o `--md` de janelas ≥ 30 dias gera ~8K chars — dentro do limite de rich message (32.768), então chega inteiro; o problema de "2 mensagens com bullets" era o caminho legacy do gateway (4.096) e foi corrigido no adapter do Telegram (fresh-final rich com `rich_all`), não no script.

## Entrega no Telegram — regra obrigatória

O output do `--md` vai **cru** no corpo da resposta do agente — verbatim, sem reformatar e **NUNCA dentro de bloco de código**. Fence de código (``` \`\`\` ```) faz o Telegram renderizar tudo como texto literal monoespaçado: as tabelas pipe nunca chegam como rich message. Não vale "proteger" o output com fence, nem cortar, nem resumir.

## Limites

- **Granularidade por sessão, não por requisição individual** — `session_model_usage` guarda contadores **acumulados por sessão**; a janela só filtra *quais* sessões entram (via `last_seen`), e os valores mostrados (reqs/tokens) são o total da sessão inteira, não do período. Exemplo real (04/09): janela de 3 min mostrava 88 reqs quando a timeline real de mensagens tinha 4.
- **Sem tokens por request** — a tabela `messages` tem granularidade por request real (timestamp, tools, reasoning, finish_reason), mas `token_count` é NULL em todas as mensagens. Para a timeline request a request, consultar `messages` direto (role='assistant' = 1 request).
- **API só expõe o % do momento** — sem histórico nem série temporal (para acumular história, cron de snapshot). Sonda de 15 endpoints (04/09): só `/zen/go/v1/usage` e `/zen/go/v1/models` existem; o console web (opencode.ai/auth) é a única fonte oficial de tracking individual.
- **%plano é estimativa** — pesos do billing (in=1, out=4, cache=0.1) não são confirmados pela OpenCode; a linha `[est]` no cabeçalho mostra a calibração usada. Se os pesos reais diferirem, a ordem de grandeza e o ranking se mantêm.
- **Sem custo em USD real** — `estimated_cost_usd` fica 0 (plano é assinatura, não pay-per-token). Os limites do plano são em $ (5h=$12, semanal=$30, mensal=$60), mas a API não devolve gasto.
- **Provider `?`** — linhas com `billing_provider` NULL no banco (ex: sessões antigas anteriores à coluna); aparecem como provider `?` e sem %plano.
- Cobre apenas o tráfego que passa pelo Hermes (state.db) — chamadas diretas de outros apps não aparecem

## Quando não usar

- Quando precisar de granularidade por requisição individual (não existe fonte — nem API nem banco)
- Para monitoramento contínuo/histórico sem intervenção (requer cron de snapshot)
- Para medição de custo monetário

## Configuração

- **Caminho:** `/root/scripts/llm-usage`
- Nada a configurar — lê `OPENCODE_GO_API_KEY` de `~/.hermes/.env` e abre o `state.db` em modo somente leitura
- **Timezone:** `America/Sao_Paulo` fixo no script (host está nesse fuso)
- **Caminhos via `HERMES_HOME`/`os.path.join`** — sem literal `~/.hermes` no arquivo (o guard do gateway lê scripts referenciados e o literal dispara falso positivo — ver [Erros conhecidos](#erros-conhecidos))

## Implementação — script v2 (verbatim)

Arquivo único executável (`#!/usr/bin/env python3`), Python stdlib, zero dependências. Fontes de dados: tabela `session_model_usage` do state.db (modo somente leitura, campo `billing_provider` discrimina o provider) + `GET https://opencode.ai/zen/go/v1/usage` do plano (exige `User-Agent` tipo curl; cliente sem UA recebe 403). O `%plano` por linha só é calculado quando o provider da linha é o do plano (`opencode-go`); a calibração usa só o tráfego desse provider no ciclo mensal (desde o `resetsAt` do monthly − 30 dias).

```python
#!/usr/bin/env python3
"""llm-usage — uso detalhado de LLMs registrados no Hermes (state.db + API OpenCode Go).

Cobre todos os providers (opencode-go, deepseek, openrouter, anthropic, nvidia,
nous, gemini, ...) — name do script não é mais só "ocg" porque o banco guarda
tráfego de vários providers. V2 do antigo ocg-usage.

- API:  /zen/go/v1/usage -> % rolling/weekly/monthly do plano OpenCode Go
- Local: state.db (HERMES_HOME) -> session_model_usage (todos os providers)

Uso:
  llm-usage [janela|data] [opts]
  janela: 15m | 30m | 1h | 3h | 6h | 12h | 1d | 3d | 1w   (default: 1h)
  data:   2026-09-01  (desde 00:00 local dessa data)
  opts:  --md | --json | --top N
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
PLAN_PROVIDER = "opencode-go"  # provider cujo monthly% vem da API

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


def md_table(headers, rows):
    print("| " + " | ".join(headers) + " |")
    print("|" + "|".join("---" for _ in headers) + "|")
    for row in rows:
        print("| " + " | ".join(str(c) for c in row) + " |")


def main():
    args = [a for a in sys.argv[1:] if not a.startswith("--")]
    opts = [a for a in sys.argv[1:] if a.startswith("--")]
    arg = args[0] if args else "1h"
    as_json = "--json" in opts
    md = "--md" in opts
    top_n = 8
    for o in opts:
        if o.startswith("--top="):
            top_n = int(o.split("=")[1])

    now = datetime.now(TZ)
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
        "SELECT s.session_id, s.billing_provider, s.model, s.api_call_count, "
        "s.input_tokens, s.output_tokens, s.cache_read_tokens, s.cache_write_tokens, "
        "s.reasoning_tokens, s.first_seen, s.last_seen, COALESCE(se.title,'') AS title "
        "FROM session_model_usage s LEFT JOIN sessions se ON se.id = s.session_id "
        "WHERE s.last_seen >= ? ORDER BY s.last_seen",
        (cutoff,),
    ).fetchall()

    # ---- calibração % do plano mensal (só tráfego do provider do plano) ----
    monthly_pct = None
    if api and api.get("usage"):
        monthly_pct = api["usage"].get("monthly", {}).get("percent")
    month_start = None
    if api and api.get("usage") and api["usage"].get("monthly", {}).get("resetsAt"):
        rst = api["usage"]["monthly"]["resetsAt"]
        try:
            month_start = datetime.fromisoformat(rst.replace("Z", "+00:00")).timestamp() - 30*86400
        except Exception:
            month_start = None
    if month_start is None:
        month_start = now.timestamp() - 30*86400
    mc = cur.execute(
        "SELECT input_tokens, output_tokens, cache_read_tokens, cache_write_tokens "
        "FROM session_model_usage WHERE last_seen >= ? AND billing_provider = ?",
        (month_start, PLAN_PROVIDER),
    ).fetchall()
    month_w = sum(weighted(r["input_tokens"], r["output_tokens"],
                           r["cache_read_tokens"], r["cache_write_tokens"]) for r in mc)
    month_raw = sum(r["input_tokens"] + r["output_tokens"] + r["cache_read_tokens"]
                    + r["cache_write_tokens"] for r in mc)
    pct_per_w = (monthly_pct / month_w) if (monthly_pct and month_w) else None

    def prov(p):
        return p or "?"  # NULL -> desconhecido

    def is_plan(p):
        return (p or "") == PLAN_PROVIDER

    def pct_s(w, p):
        if pct_per_w and is_plan(p):
            return f"{pct_per_w * w:.2f}%"
        return "-"

    # ---- agregados: chave (dia, provider, modelo) ----
    cells, day_tot, model_tot, sess = {}, {}, {}, {}
    for r in rows:
        d = datetime.fromtimestamp(r["last_seen"], TZ).date()
        fday = lambda x: x.strftime("%d/%m (%a)")
        p = prov(r["billing_provider"])
        key = (d, p, r["model"])
        c = cells.setdefault(key, {"sess": set(), "reqs": 0, "in": 0, "out": 0,
                                   "cr": 0, "cw": 0, "rz": 0})
        c["sess"].add(r["session_id"])
        c["reqs"] += r["api_call_count"]
        c["in"] += r["input_tokens"]
        c["out"] += r["output_tokens"]
        c["cr"] += r["cache_read_tokens"]
        c["cw"] += r["cache_write_tokens"]
        c["rz"] += r["reasoning_tokens"]
        for sc in (day_tot.setdefault(d, {}), model_tot.setdefault((p, r["model"]), {})):
            t = sc.setdefault("sess", set())
            t.add(r["session_id"])
            for k, v in (("reqs", r["api_call_count"]), ("in", r["input_tokens"]),
                         ("out", r["output_tokens"]), ("cr", r["cache_read_tokens"]),
                         ("cw", r["cache_write_tokens"]), ("rz", r["reasoning_tokens"])):
                sc[k] = sc.get(k, 0) + v
        s = sess.setdefault(r["session_id"], {"models": set(), "tasks": set(),
            "reqs": 0, "in": 0, "out": 0, "cr": 0, "cw": 0, "rz": 0,
            "first": r["first_seen"], "last": r["last_seen"], "title": r["title"] or ""})
        s["models"].add(f"{p}/{r['model']}")
        s["reqs"] += r["api_call_count"]
        s["in"] += r["input_tokens"]
        s["out"] += r["output_tokens"]
        s["cr"] += r["cache_read_tokens"]
        s["cw"] += r["cache_write_tokens"]
        s["rz"] += r["reasoning_tokens"]
        s["first"] = min(s["first"], r["first_seen"])
        s["last"] = max(s["last"], r["last_seen"])

    print(f"LLM usage (state.db) — {cutoff_dt:%d/%m/%Y %H:%M} → {now:%d/%m %H:%M} ({TZ})")
    if api and api.get("usage"):
        u = api["usage"]
        for k in ("rolling", "weekly", "monthly"):
            v = u.get(k, {})
            reset = v.get("resetsAt", "")[:16].replace("T", " ")
            print(f"plano {k:<8} {v.get('percent', '?')}%  (reset {reset} UTC) [provider {PLAN_PROVIDER}]")
    else:
        print(f"plano: API indisponível ({api.get('error') if isinstance(api, dict) else ''})")
    print(f"[est] ciclo mensal ({PLAN_PROVIDER}): {fmt(month_raw)} brutos / {fmt(month_w)} pond. "
          f"— calibração do %plano contra monthly {monthly_pct}%")

    if not rows:
        print("\nsem atividade no período")
        return

    # ---- GRID dia × provider × modelo ----
    grid_rows = []
    grand = {"sess": set(), "reqs": 0, "in": 0, "out": 0, "cr": 0, "cw": 0, "rz": 0, "w": 0.0}
    for d in sorted(day_tot.keys(), reverse=True):
        for p, m in sorted({(pp, mm) for (dd, pp, mm) in cells if dd == d},
                           key=lambda x: -cells[(d, x[0], x[1])]["reqs"]):
            c = cells[(d, p, m)]
            w = weighted(c["in"], c["out"], c["cr"], c["cw"])
            grid_rows.append((fday(d), p, m, len(c["sess"]), c["reqs"], fmt(c["in"]),
                              fmt(c["out"]), fmt(c["cr"]), fmt(c["cw"]), fmt(c["rz"]),
                              fmt(w), pct_s(w, p)))
            grand["sess"] |= c["sess"]
            for k in ("reqs", "in", "out", "cr", "cw", "rz"):
                grand[k] += c[k]
            grand["w"] += w
    w_g = weighted(grand["in"], grand["out"], grand["cr"], grand["cw"])
    if md:
        print(f"\n## GRID dia × provider × modelo (pesos: in={W_IN}x out={W_OUT}x cache={W_CACHE}x)")
        md_table(("dia", "provider", "modelo", "sessões", "reqs", "input", "output",
                  "cacheR", "cacheW", "reason", "pond.", "%plano"), grid_rows)
    else:
        print(f"\n{'='*100}\nGRID dia × provider × modelo (pesos: in={W_IN}x out={W_OUT}x cache={W_CACHE}x)\n{'='*100}")
        print(f"{'dia':<13}{'provider':<14}{'modelo':<24}{'sessões':>7}{'reqs':>6}{'input':>9}"
              f"{'output':>9}{'cacheR':>9}{'cacheW':>8}{'reason':>8}{'pond.':>10}{'%plano':>8}")
        for row in grid_rows:
            print(f"{row[0]:<13}{row[1]:<14}{row[2]:<24}{row[3]:>7}{row[4]:>6}{row[5]:>9}"
                  f"{row[6]:>9}{row[7]:>9}{row[8]:>8}{row[9]:>8}{row[10]:>10}{row[11]:>8}")

    # ---- Σ POR DIA ----
    day_rows = []
    for d in sorted(day_tot.keys(), reverse=True):
        t = day_tot[d]
        w = weighted(t["in"], t["out"], t["cr"], t["cw"])
        day_rows.append((fday(d), len(t["sess"]), t["reqs"], fmt(t["in"]), fmt(t["out"]),
                         fmt(t["cr"]), fmt(t["cw"]), fmt(t["rz"]), fmt(w), pct_s(w, PLAN_PROVIDER)))
    day_rows.append(("Σ TOTAL", len(grand["sess"]), grand["reqs"], fmt(grand["in"]),
                     fmt(grand["out"]), fmt(grand["cr"]), fmt(grand["cw"]), fmt(grand["rz"]),
                     fmt(w_g), pct_s(w_g, PLAN_PROVIDER)))
    if md:
        print("\n## Σ POR DIA")
        md_table(("dia", "sessões", "reqs", "input", "output", "cacheR",
                  "cacheW", "reason", "pond.", "%plano"), day_rows)
    else:
        print(f"\n{'─'*90}\nΣ POR DIA\n{'─'*90}")
        print(f"{'dia':<13}{'sessões':>7}{'reqs':>6}{'input':>9}{'output':>9}{'cacheR':>9}"
              f"{'cacheW':>8}{'reason':>8}{'pond.':>10}{'%plano':>8}")
        for row in day_rows[:-1]:
            print(f"{row[0]:<13}{row[1]:>7}{row[2]:>6}{row[3]:>9}{row[4]:>9}"
                  f"{row[5]:>9}{row[6]:>8}{row[7]:>8}{row[8]:>10}{row[9]:>8}")
        print(f"{day_rows[-1][0]:<13}{day_rows[-1][1]:>7}{day_rows[-1][2]:>6}{day_rows[-1][3]:>9}"
              f"{day_rows[-1][4]:>9}{day_rows[-1][5]:>9}{day_rows[-1][6]:>8}{day_rows[-1][7]:>8}"
              f"{day_rows[-1][8]:>10}{day_rows[-1][9]:>8}")

    # ---- Σ POR PROVIDER (agregado: todos os modelos do provider) ----
    prov_tot = {}
    for (p, m), t in model_tot.items():
        pt = prov_tot.setdefault(p, {"sess": set(), "reqs": 0, "in": 0, "out": 0,
                                     "cr": 0, "cw": 0, "rz": 0})
        pt["sess"] |= t["sess"]
        for k in ("reqs", "in", "out", "cr", "cw", "rz"):
            pt[k] = pt.get(k, 0) + t[k]
    prov_rows = []
    for p in sorted(prov_tot.keys(), key=lambda x: -prov_tot[x]["reqs"]):
        t = prov_tot[p]
        w = weighted(t["in"], t["out"], t["cr"], t["cw"])
        prov_rows.append((p, len(t["sess"]), t["reqs"], fmt(t["in"]), fmt(t["out"]),
                          fmt(t["cr"]), fmt(t["cw"]), fmt(t["rz"]), fmt(w), pct_s(w, p)))
    if md:
        print("\n## Σ POR PROVIDER (todos os modelos somados)")
        md_table(("provider", "sessões", "reqs", "input", "output", "cacheR",
                  "cacheW", "reason", "pond.", "%plano"), prov_rows)
    else:
        print(f"\n{'─'*90}\nΣ POR PROVIDER (todos os modelos somados)\n{'─'*90}")
        print(f"{'provider':<14}{'sessões':>7}{'reqs':>6}{'input':>9}{'output':>9}{'cacheR':>9}"
              f"{'cacheW':>8}{'reason':>8}{'pond.':>10}{'%plano':>8}")
        for row in prov_rows:
            print(f"{row[0]:<14}{row[1]:>7}{row[2]:>6}{row[3]:>9}{row[4]:>9}"
                  f"{row[5]:>9}{row[6]:>8}{row[7]:>8}{row[8]:>10}{row[9]:>8}")

    # ---- SUBTOTAL POR PROVIDER / MODELO ----
    model_rows = []
    for (p, m) in sorted(model_tot.keys(), key=lambda x: -model_tot[x].get("reqs", 0)):
        t = model_tot[(p, m)]
        w = weighted(t["in"], t["out"], t["cr"], t["cw"])
        model_rows.append((p, m, len(t["sess"]), t["reqs"], fmt(t["in"]), fmt(t["out"]),
                           fmt(t["cr"]), fmt(t["cw"]), fmt(t["rz"]), fmt(w), pct_s(w, p)))
    if md:
        print("\n## SUBTOTAL POR PROVIDER / MODELO")
        md_table(("provider", "modelo", "sessões", "reqs", "input", "output", "cacheR",
                  "cacheW", "reason", "pond.", "%plano"), model_rows)
    else:
        print(f"\n{'─'*90}\nSUBTOTAL POR PROVIDER / MODELO\n{'─'*90}")
        print(f"{'provider':<14}{'modelo':<24}{'sessões':>7}{'reqs':>6}{'input':>9}{'output':>9}"
              f"{'cacheR':>9}{'cacheW':>8}{'reason':>8}{'pond.':>10}{'%plano':>8}")
        for row in model_rows:
            print(f"{row[0]:<14}{row[1]:<24}{row[2]:>7}{row[3]:>6}{row[4]:>9}{row[5]:>9}"
                  f"{row[6]:>9}{row[7]:>8}{row[8]:>8}{row[9]:>10}{row[10]:>8}")

    # ---- SESSÕES ----
    sess_rows = []
    for sid, s in sorted(sess.items(), key=lambda x: -x[1]["reqs"])[:top_n]:
        span = (s["last"] - s["first"]) / 60
        title = (s["title"] or "")[:40]
        w = weighted(s["in"], s["out"], s["cr"], s["cw"])
        sess_rows.append((datetime.fromtimestamp(s["last"], TZ).strftime("%d/%m %H:%M"),
                          s["reqs"], fmt(s["in"]), fmt(s["out"]), fmt(s["cr"]),
                          fmt(w), pct_s(w, PLAN_PROVIDER), ",".join(sorted(s["models"])),
                          sid[-8:], title))
    if md:
        print(f"\n## SESSÕES ({len(sess)})")
        md_table(("horário", "reqs", "input", "output", "cacheR", "pond.",
                  "%plano", "modelos (provider/modelo)", "sessão", "título"), sess_rows)
    else:
        print(f"\n{'─'*90}\nSESSÕES ({len(sess)})\n{'─'*90}")
        for row in sess_rows:
            print(f"{row[0]}  {row[1]:>5} reqs  {row[2]} in / {row[3]} out / {row[4]} cR  "
                  f"pond~{row[5]}  {row[6]}  {row[7]}  {row[8]}  {row[9]}")

    if as_json:
        out = {
            "window": arg, "from": cutoff_dt.isoformat(), "to": now.isoformat(),
            "api": api, "monthly_pct": monthly_pct, "plan_provider": PLAN_PROVIDER,
            "weights": {"in": W_IN, "out": W_OUT, "cache": W_CACHE},
            "by_provider_model": {f"{p}|{m}": {**{k: (len(v) if k == "sess" else v) for k, v in t.items()},
                                                "pond": weighted(t["in"], t["out"], t["cr"], t["cw"])}
                                  for (p, m), t in model_tot.items()},
            "by_day": {d.isoformat(): {**{k: (len(v) if k == "sess" else v) for k, v in t.items()},
                                       "pond": weighted(t["in"], t["out"], t["cr"], t["cw"])}
                       for d, t in day_tot.items()},
            "cells": {f"{d.isoformat()}|{p}|{m}": {k: (len(v) if k == "sess" else v) for k, v in c.items()}
                      for (d, p, m), c in cells.items()},
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
| Janela inválida | Parâmetro fora da lista / data malformada | Mensagem de erro lista as janelas válidas (exit 2) |
| Linha "plano" indisponível | API de uso fora do ar / sem chave no .env | Resto do relatório continua; comportamento de degradação |
| Coluna %plano em "-" | Provider da linha ≠ opencode-go, ou calibração falhou (API sem monthly% / sem dados do ciclo) | O grid segue completo, sem a estimativa |
| **v1 (`ocg-usage`) bloqueada pelo guard do gateway** | O guard (`cron/lifecycle_guard.py`) lê scripts referenciados, tokeniza o código como shell e o literal `~/.hermes/state.db` da v1 vira "script referenciado" → tenta ler os ~205MB do state.db e estoura o limite de 1MB (fail-closed) | Usar o `llm-usage` (v2), que monta caminhos via `HERMES_HOME`/`os.path.join`, sem o literal |

## Status de validação

- `stable` — v2 oficial **validada ao vivo pelo usuário em 2026-09-04**: janelas `15m`/`3h`/`7d`/`15d`/`25d`/`30d`/`31d`/`32d` (`32d` = 7.977 chars chegou numa única mensagem rich pós-fix do adapter), `2026-09-01` (desde data), `--md`, `--json`, guard do gateway `False` (destravado)

## Conexões

- [[wiki/tools/llm-providers.md|LLM Providers]] — o provider OpenCode Go (e demais) que esta ferramenta monitora
- [[wiki/systems/hermes.md|Hermes]] — grava os dados em `session_model_usage` (com `billing_provider`) no `state.db`; a timeline request a request vem da tabela `messages`