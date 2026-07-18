---
type: tool
tags: [tools, autoloop, agentes, loops, orquestracao, claude-code]
title: autoloop
description: Harness de loops autônomos de agentes LLM (inspirado no autoresearch do Karpathy) — presets com papéis distintos, dashboard web local, controle de custo/iterações; testado em 2026-07-18 (caro e lento para tarefas simples — ver Lições aprendidas)
timestamp: 2026-07-18T18:30:00-03:00
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

**ATENÇÃO — o preset sobrepõe o toml do projeto.** O preset `autocode` traz `max_iterations = 100` embutido, que vence o `autoloops.toml` da raiz. Limites reais devem ser gravados como override por repositório: `autoloop config set --repo --preset autocode event_loop.max_iterations=10` (grava em `.autoloop/overrides/autocode.toml`). Flags do backend também vão ali, via `backend.args` (não concatenadas em `backend.command` — comando com espaços quebra com "not found").

Acesso ao dashboard a partir do PC: proxy com senha em `http://<ip-da-vps>:3900` (script `/root/scripts/dashboard-proxy.js`, HTTP Basic Auth, repassa para o dashboard em localhost:3778; não sobrevive a reboot). Túnel SSH e port-forward do VS Code também funcionam, mas se mostraram frágeis.

Papéis customizados: `autoloop init --preset <nome>` cria `presets/<nome>/` com `topology.toml` + `roles/*.md` (prompts em Markdown editáveis).

## Como funciona por dentro (verificado no código)

- **Cada iteração = sessão NOVA do Claude em modo headless** (`claude -p`), sem `--resume`/`--continue` e sem subagentes. As sessões não têm memória umas das outras; a continuidade vem dos arquivos no disco, do journal (`.autoloop/journal.jsonl`) e de até 8.000 chars de memória destilada injetada no prompt ("disco é estado, git é memória" — mesma filosofia do ralph-orchestrator).
- Cada ativação de papel (planner/builder/critic/finalizer) conta como **uma iteração** — o caminho feliz mínimo do `autocode` já gasta 4.
- O preset invoca o Claude com `--dangerously-skip-permissions` — o agente do loop **não pede permissão para nada**. Worktree/usuário isolado não é opcional.

## Lições aprendidas (teste real de 2026-07-18)

Teste: criar 3 templates visuais HyperFrames (tarefa que em chat direto leva segundos). Resultado: 1 template bom entregue, 5 runs disparados, ~8 min no run que completou, **≈ US$ 7,80 em equivalente-API** (Sonnet 5, medido nos transcripts de `/home/looper/.claude/projects/`) — consumido da cota do plano.

1. **Custo é dominado pelo "pedágio da sessão fria"**: dos ~$7,80, mais de 70% foi contexto recarregado (15,9M tokens de cache lido + 974k escritos em 9 sessões) — catálogo de ~30 skills, MCPs, memória, plano e releitura do projeto a cada acordar. O trabalho útil (214k tokens de saída) foi minoria.
2. **Claude recusa `--dangerously-skip-permissions` como root** — obrigatório rodar loops como usuário comum. Criado o usuário `looper` (`/home/looper`), com credenciais do Claude copiadas e cache do Chrome/fontes do hyperframes em `/home/looper/.cache/hyperframes/`.
3. **Sem MCPs no loop**: `backend.args` com `--strict-mcp-config --mcp-config '{"mcpServers":{}}'` (o JSON `{}` puro é rejeitado — precisa da chave `mcpServers`). Corta ~90 tools de Trello/n8n do contexto e elimina o risco de um loop autônomo mexer neles sem pedir permissão.
4. **Timeout por iteração precisa de folga**: 300s decapitou o builder duas vezes (o trabalho parcial fica no disco; `autoloop resume` continua). Iterações reais de construção levam vários minutos.
5. **Nunca 2 loops em paralelo nesta VPS** (2 vCPUs): as sessões competem por CPU e se derrubam por timeout.
6. **Custo por iteração NÃO é capturado por padrão** — `claude -p` texto puro não devolve usage; journal e `stats` mostram $0. Exige `--output-format json` + `backend.usage_from` (não configurado ainda). Sem isso, `max_cost_usd` não funciona e o consumo fica invisível.
7. **Ambiente deve estar pré-preparado**: scaffold, dependências e navegador instalados ANTES do run — preparar ambiente é trabalho do operador, não do loop (senão a 1ª iteração morre baixando dependências).
8. **Veredito**: para tarefas pequenas/médias, chat direto é ordens de magnitude mais rápido e barato. Loop só compensa em tarefa longa e repetitiva, com critério objetivo de pronto, rodando sem supervisão — e idealmente num usuário com poucas skills/MCPs instalados (menos pedágio por sessão).

## Quando não usar

- Tarefa que exige aprovação humana a cada ação individual (cada comando/edição) — isso é o Claude Code interativo, não um loop autônomo. O controle do autoloop é por rodada, não por ação
- **Tarefa pequena ou média** — o overhead de sessões frias custa mais que o trabalho (ver Lições aprendidas)
- Mais de um loop simultâneo nesta VPS

## Configuração

- Binário global: `autoloop` (npm, pacote `@mobrienv/autoloop`)
- Config por projeto: `autoloops.toml` na raiz; **limites efetivos** em `.autoloop/overrides/<preset>.toml` (ver aviso em Como usar); estado do run em `.autoloop/` (gitignorado)
- Presets empacotados em `/usr/lib/node_modules/@mobrienv/autoloop/node_modules/@mobrienv/autoloop-presets/presets/`
- **Loops rodam como o usuário `looper`** (nunca root); projeto de loops em `/home/looper/projects/Loop 1/` (com `template-1/2/3`, cada um repo git próprio com overrides: 10 iterações, 300s, stall 3, sem MCPs)
- Dashboard com senha: `node /root/scripts/dashboard-proxy.js` (porta 3900 → 3778); credenciais no próprio script

## Erros conhecidos

- `command = "claude --flags"` no toml → "not found" (o comando é executado como um binário único; flags vão em `backend.args`)
- `--mcp-config '{}'` → "mcpServers: Invalid input" (usar `'{"mcpServers":{}}'`)
- `--dangerously-skip-permissions` como root → recusado pelo Claude ("cannot be used with root/sudo")
- `backend_timeout` mata o run inteiro (não só a iteração); retomar com `autoloop resume <run-id>`
- Dashboard reiniciado derruba o port-forward automático do VS Code do PC do Giovani (motivo do proxy na 3900)

## Status de validação

- ✅ Loop real executado e completado (2026-07-18, run `clear-source` no template-1: planner→builder→critic→finalizer, portão de evidências, `task.complete`) — frame HyperFrames válido produzido
- ✅ Verificados na prática: overrides de preset, isolamento do usuário `looper`, exclusão de MCPs, dashboard, journal, interrupção por timeout e por stall
- ⚠️ Não testados: worktree, `control guide/respond`, `resume`, captura de custo via `usage_from`, `max_cost_usd`

## Conexões

- [[wiki/systems/vps.md|VPS]] — onde o autoloop está instalado; o backend padrão é o Claude Code da VPS
- [[wiki/concepts/okf.md|OKF]] — o preset `autowiki` gera páginas no mesmo padrão OKF desta wiki
