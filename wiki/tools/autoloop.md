---
type: tool
tags: [tools, autoloop, agentes, loops, orquestracao, claude-code]
title: autoloop
description: Harness de loops autônomos de agentes LLM (inspirado no autoresearch do Karpathy) — presets com papéis distintos, dashboard web local, controle de custo/iterações; instalado globalmente na VPS em 2026-07-18, ainda sem execução real
timestamp: 2026-07-18T13:30:00-03:00
status: draft
---

# autoloop

## O que é

CLI open-source ([mikeyobrien/autoloop](https://github.com/mikeyobrien/autoloop), MIT, TypeScript) que roda **loops autônomos de agentes LLM** contra um projeto: você aponta um preset (receita de loop) e uma tarefa, e ele itera um agente de backend (por padrão o Claude Code da VPS) até concluir. É um derivado simplificado do ralph-orchestrator, do mesmo autor, inspirado no padrão autoresearch do Karpathy (dar aos agentes um objetivo mensurável e deixá-los iterar sozinhos).

Instalado globalmente na VPS via `npm install -g @mobrienv/autoloop` em 2026-07-18. Versão instalada: **0.9.2**.

## Capabilities

- **Presets prontos** (`autoloop list`): `autocode` (implementar features), `autofix` (bugs), `autoresearch` (explorar questão técnica por experimentos), `autoqa`, `autotest`, `autosec`, `autoperf`, `autodoc`, `autoreview`, `autospec`, `autosimplify`, `autoideas`, `automerge`, `autopr`, `autowiki` (transforma fila de URLs em páginas wiki no padrão OKF — o mesmo desta wiki)
- **Papéis distintos por loop**: cada preset define uma topologia de roles com prompts separados (um não tem o contexto do outro). Ex. `autocode`: `planner` → `builder` → `critic` → `finalizer`, conectados por eventos (`review.ready`, `review.rejected`...). Entre critic e finalizer há um **portão de evidências**: aprovação só vale com testes, lint e typecheck passando (cobertura mínima 80%)
- **Limites de segurança** no `autoloops.toml`: `max_iterations`, `max_cost_usd` (teto de gasto), `max_runtime`, `max_iteration_runtime`
- **Worktree isolado**: o agente trabalha numa cópia; nada entra no projeto real sem `autoloop worktree diff` + `worktree merge` manual
- **Controle ao vivo**: `control interrupt` (frear), `control guide "instrução"` (direcionar próxima iteração), `control respond` (responder pergunta que o agente fez via `human.ask`)
- **Dashboard web local** (`autoloop dashboard --port <porta>`) + `loops watch <run-id>` no terminal
- **Aprovação rodada a rodada**: `max_iterations = 1` + `autoloop resume <run-id> --add-iterations 1`
- Hooks de ciclo de vida (`pre_iteration`, `post_iteration`...) que suspendem o loop se falharem
- Saída estruturada em tudo: `--json` em `loops`, `stats`, `triage`, `doctor`; handbook para agentes via `autoloop robot-docs`

## Limites

- **Dashboard sem autenticação** — escuta só em `127.0.0.1` por padrão; acesso remoto exige túnel SSH. Nunca expor em `0.0.0.0`
- UI do dashboard é funcional porém simples (longe de dashboards ricos como o "Helix v2" visto em demos de terceiros)
- `max_iteration_runtime` curto (ex. 60s) corta a iteração do Claude no meio — iterações reais levam minutos
- Projeto pequeno (~67 stars em 2026-07), mantenedor único

## Como usar

```bash
cd <projeto>
autoloop init                      # cria autoloops.toml comentado
autoloop run autocode "tarefa"     # dispara o loop
autoloop loops                     # execuções ativas
autoloop loops watch <run-id>      # acompanhar ao vivo
autoloop resume <run-id> --add-iterations 1   # liberar mais uma rodada
autoloop dashboard --port 3778     # UI web (localhost)
autoloop stats                     # custos e taxa de sucesso por preset
```

Parâmetros que o Giovani pode editar sozinho no `autoloops.toml` (qualquer editor de texto):

```toml
[event_loop]
max_iterations = 1          # rodadas antes de parar
max_cost_usd = 2            # teto de gasto (0 = sem limite)
max_iteration_runtime = "60s"  # tempo máximo por rodada
[backend]
command = "claude"          # motor do loop
```

Acesso ao dashboard a partir do PC (túnel SSH): `ssh -N -L 3778:localhost:3778 root@<ip-da-vps>` e abrir `http://localhost:3778`.

Papéis customizados: `autoloop init --preset <nome>` cria `presets/<nome>/` com `topology.toml` + `roles/*.md` (prompts em Markdown editáveis).

## Quando não usar

- Tarefa que exige aprovação humana a cada ação individual (cada comando/edição) — isso é o Claude Code interativo, não um loop autônomo. O controle do autoloop é por rodada, não por ação
- Tarefa pequena de um passo só — o overhead do loop não compensa

## Configuração

- Binário global: `autoloop` (npm, pacote `@mobrienv/autoloop`)
- Config por projeto: `autoloops.toml` na raiz (criado por `autoloop init`); estado do run em `.autoloop/` (gitignorado)
- Presets empacotados em `/usr/lib/node_modules/@mobrienv/autoloop/node_modules/@mobrienv/autoloop-presets/presets/`
- Projeto de teste preparado em `/root/autoloop-teste` (config `max_iterations = 1`, `max_iteration_runtime = "60s"`; nenhum run executado)

## Erros conhecidos

- Nenhum até agora — nenhuma execução real ainda.

## Status de validação

- ✅ Verificado na VPS (2026-07-18): instalação, `--version`, `--help`, `list`, `config show`, dashboard sobe na porta 3778, estrutura de roles/topologia do preset `autocode` lida nos arquivos
- ⚠️ **Nunca executado um loop real** — capabilities de execução (worktree, control, resume, custos, dashboard com dados) vêm da documentação oficial, não de teste próprio. Giovani vai testar quando for oportuno

## Conexões

- [[wiki/systems/vps.md|VPS]] — onde o autoloop está instalado; o backend padrão é o Claude Code da VPS
- [[wiki/concepts/okf.md|OKF]] — o preset `autowiki` gera páginas no mesmo padrão OKF desta wiki
