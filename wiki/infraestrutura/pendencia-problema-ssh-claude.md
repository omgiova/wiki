---
type: session
tags: [hermes, claude, ssh, termux, remote-control, bug]
title: Problema SSH/Claude — Sessões Travando no Remote Control
description: Diagnóstico de 3 problemas que causam travamento de sessões Claude via Remote Control (Termux/Android) — curl sem timeout, processo zumbi e prompt de aprovação invisível
timestamp: 2026-06-26T00:00:00-03:00
status: draft
---

# Problema SSH/Claude — Sessões Travando no Remote Control

Diagnosticado em 2026-06-26. Explica por que sessões do Claude via Remote Control aparecem como "Executando..." indefinidamente.

## Problema 1 — curl sem --max-time no script de startup

**Causa direta do travamento.**

O script de startup do Hermes executava `curl` sem `--max-time` para buscar dados de MCPs/skills/webhooks. Quando a chamada travou (sem resposta do servidor), o Claude ficou bloqueado esperando o bash terminar — indefinidamente.

**Fix:**
```bash
# Antes (problemático)
curl https://...

# Depois
curl --max-time 10 https://...
```

Aplicar em todos os `curl` dentro de scripts que rodam no startup.

**Status:** Pendente aplicar na VPS.

---

## Problema 2 — Processo zumbi da sessão anterior (pts/0)

Quando uma sessão é "cancelada" pelo app, o processo no terminal (pts/0 no Termux) **não morre**. O Claude fica em estado `sleeping`, sem filhos (script já morreu), mas ainda ocupando memória.

**Sintoma:** `ps aux | grep claude` mostra processo de sessão anterior ainda vivo.

**Referência:** PID 3382543 identificado na sessão de diagnóstico.

**Riscos:**
- Consumo de memória acumulado com múltiplas sessões canceladas
- Potencial conflito com sessões futuras

**Fix imediato:**
```bash
kill <PID>
# ou para limpar todos os processos zumbi do claude:
pkill -f "claude"
```

**Fix permanente:** Configurar script de cleanup que mata processos órfãos do Claude ao iniciar nova sessão.

**Status:** Pendente implementar o cleanup automático.

---

## Problema 3 — Prompt de aprovação invisível no Remote Control

**Explica a maioria dos "travamentos" históricos.**

Quando o Claude precisa de aprovação para uma ferramenta, o prompt aparece **no terminal** (pts/0, Termux). Mas o usuário está olhando para o **app Claude no celular**, que só mostra "Executando..." — sem nenhum prompt visível.

```
App Claude (celular) → mostra "Executando..."
Terminal Termux      → mostra [y/n] aguardando resposta
```

Resultado: sessão aparentemente travada sem forma de responder, a não ser abrir o Termux e responder manualmente.

**Workaround atual:** Abrir o Termux e verificar se há prompt pendente.

**Possíveis soluções (não implementadas):**
- Configurar permissões automáticas para tools que não precisam de aprovação manual
- Investigar se Remote Control tem alguma forma de expor o prompt no app

**Status:** Pendente investigar solução definitiva.

---

## Problema Extra — ai-memory server down

O servidor `ai-memory` (porta 49374) estava down no momento do diagnóstico.

- MCP ai-memory desabilitado (migração para wiki Karpathy já feita — ver [[pendencias/proximos-passos.md]])
- Não causa travamento (hooks têm timeout de 0.5s)
- Pode ser ignorado se a migração para wiki estiver completa

**Status:** Provavelmente irrelevante após migração.

---

## Resumo de prioridades

| # | Problema | Impacto | Esforço | Status |
|---|---|---|---|---|
| 1 | curl sem --max-time | Alto — trava sessão | Baixo | Pendente |
| 3 | Prompt invisível no app | Alto — travamento frequente | Médio | Pendente investigar |
| 2 | Processo zumbi | Baixo — memória | Baixo | Pendente |
