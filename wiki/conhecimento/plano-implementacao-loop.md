---
type: concept
tags: [agentes, loops, arquitetura, loop-engineering, planejamento]
title: Plano de Implementação — Loops Agênticos
description: Base de conhecimento e planejamento para construção de sistemas de loop autônomo para o Hermes. Fundamentado em fontes primárias rastreáveis.
timestamp: 2026-06-27T02:10:00-03:00
status: draft
---

# Plano de Implementação — Loops Agênticos

## Tese central

> *"You shouldn't be prompting coding agents anymore. You should be designing loops that prompt your agents."*
> — Peter Steinberger (@steipete), criador do OpenClaw, agora na OpenAI
> Fonte: https://x.com/steipete/status/2063697162748260627 (~junho 2026, 6,5M views)

Complementada por:

> *"I don't prompt Claude anymore. I have loops running that prompt Claude and figuring out what to do. My job is to write loops."*
> — Boris Cherny, head of Claude Code na Anthropic
> Fonte primária: https://www.youtube.com/watch?v=SlGRN8jh2RI
> Fonte secundária: https://thenewstack.io/loop-engineering/

O ponto de alavancagem mudou: não é mais escrever o prompt certo — é escrever o sistema que dispara os prompts.

---

## O que é loop engineering

Baseado em: Addy Osmani, *"Loop Engineering"*
- Blog: https://addyosmani.com/blog/loop-engineering/ (7 jun 2026)
- O'Reilly Radar: https://www.oreilly.com/radar/loop-engineering/ (22 jun 2026)
- Autor: ex-Director Google Cloud AI, autor de *Beyond Vibe Coding* (O'Reilly)

Definição de Osmani:
> *"Replacing yourself as the person who prompts the agent. You design the system that does it instead."*

### Os 6 componentes de um loop (segundo Osmani)

| # | Componente | O que é |
|---|---|---|
| 1 | **Automations** | o que dispara o loop (cron, evento, condição) |
| 2 | **Worktrees** | ambiente isolado onde o agente opera |
| 3 | **Skills** | capacidades específicas disponíveis ao agente |
| 4 | **Plugins/connectors** | integrações com sistemas externos |
| 5 | **Subagents** | agentes especializados para tarefas específicas |
| 6 | **Memory/state** | o que persiste entre execuções |

### Princípios extraídos das fontes

**Maker/checker** — o modelo que produziu algo não deve revisar o próprio trabalho:
> *"The model that wrote the code is way too nice grading its own homework. A second agent with different instructions and sometimes a different model catches the stuff the first one talked itself into."*

**Memória em disco, não em contexto:**
> *"The model forgets everything between runs so the memory has to be on disk and not in the context. The agent forgets; the repo doesn't."*

**Condição de parada verificada por agente separado:**
> O `/goal` do Claude Code *"keeps going until a condition you wrote is actually true, and after every turn a separate small model checks whether you are done"* — maker e checker são agentes distintos, inclusive para decidir quando parar.

**Aviso real:**
> *"A loop running unattended is also a loop making mistakes unattended."*

---

## OpenClaw como referência de implementação

### O que é o OpenClaw
- **Repositório:** https://github.com/openclaw/openclaw
- **Autor:** Peter Steinberger + comunidade
- **Stack:** TypeScript/Node.js
- **Popularidade:** 381.000 stars no GitHub (um dos mais estrelados da história)
- **Status:** open source, ativo (último commit 27 jun 2026)

Filosofia central (fonte: Paolo Perazzo, https://ppaolo.substack.com/p/openclaw-system-architecture-overview, 11 fev 2026):
> *"OpenClaw treats AI as an infrastructure problem: sessions, memory, tool sandboxing, access control, and orchestration. The LLM provides intelligence; OpenClaw provides the execution environment."*

### Arquitetura (hub-and-spoke)

Gateway central em WebSocket (`127.0.0.1:18789`) como control plane. Todos os canais (WhatsApp, Telegram, Discord, CLI, Web UI) conectam nele — mesma filosofia do Hermes.

**Agent Runtime** (`src/agents/piembeddedrunner.ts`) — a cada turno faz 4 coisas:
1. Resolve sessão
2. Monta contexto
3. Streama resposta e executa tool calls
4. Persiste estado

**Decisões de design relevantes:**
- System prompt como **stack de arquivos**, não string única — composição de múltiplas configs do workspace
- **Idempotency obrigatório:** toda operação com efeito colateral requer idempotency key — retry seguro sem ações duplicadas
- **Cron jobs escopados por agente** — cada agente tem seus próprios cron jobs, não compartilhados (commit #96883)
- **Session isolation:** tipos de sessão com permissões diferentes; `main` pode rodar tools no host, sessões `dm`/`group` têm sandbox mais restrito
- **Plugin system:** 4 tipos (channel, memory, tool, provider), discovery automático via `package.json`

### Sistema de detecção de loop (issue #57263)

Referência: única documentação pública detalhada — issue no repositório. Código-fonte (`src/agents/`) não acessível sem autenticação GitHub.

**5 detectores:**

| Detector | O que detecta | Warning | Bloqueio |
|---|---|---|---|
| `generic_repeat` | mesma tool + args + resultado idêntico | 10 calls | 20 calls |
| `unknown_tool_repeat` | tool inexistente chamada repetidamente | — | 10 calls |
| `known_poll_no_progress` | poll/log sem progresso real | 10 calls | 20 calls |
| `global_circuit_breaker` | qualquer tool 30+ vezes sem progresso | — | 30 calls (hard block) |
| `ping_pong` | alternância A→B→A→B sem resultado novo | 10 calls | 20 calls |

**Tool Outcome Hashing:** `sha256(args_sem_volatile + resultado)` — só é "loop" se o **resultado também não mudou**. Campos voláteis (`timestamp`, `messageId`, `runId`) são removidos antes do hash.

**Arquivos referenciados no issue** (não acessíveis publicamente):
- `src/agents/tool-loop-detection.ts`
- `src/agents/embedded-agent-runner/run.ts`
- `src/agents/bash-tools.exec-approval-followup.ts`
- `src/agents/bash-tools.exec-approval-followup-state.ts`
- `src/agents/execution-contract.ts`
- `src/agents/post-compaction-loop-guard.ts`

---

## Estado atual do Hermes

### O que já tem
- `delegate_task` — spawn de subagentes (paralelo, isolado)
- `cronjob` — loops agendados
- `execute_code` — pipelines multi-tool
- `max_turns` (default 90) — único hard stop

### O que não tem (gaps confirmados)
- ❌ Loop detector com hash de args + resultado
- ❌ Idempotency key em operações com efeito colateral
- ❌ Maker/checker (agente que valida o trabalho de outro)
- ❌ Strict-agentic guard (bloquear se só planejar sem agir)
- ❌ Circuit breaker com sliding window (além do max_turns global)
- ❌ Post-compaction loop guard
- ❌ Async approval followup
- ❌ Prompt caching (`cache_control`) — 85–90% de economia em input tokens
- ❌ Cron jobs escopados por agente

---

## Loop externo com Claude Code CLI na VPS

> Testado e validado em 2026-06-27 (Claude Code v2.1.183, autenticação OAuth).

O binário `claude` pode ser acionado pelo cron do sistema sem nenhuma sessão interativa aberta. Credenciais OAuth ficam em `~/.claude/.credentials.json` e são lidas automaticamente.

**Resultado dos testes:**

| Comando | Ambiente mínimo (env -i) | Resultado |
|---|---|---|
| `claude --bare -p "..."` | HOME=/root PATH=... | ❌ "Not logged in" |
| `claude -p "..."` | HOME=/root PATH=... | ✅ funciona |

`--bare` é incompatível com autenticação OAuth — assume `ANTHROPIC_API_KEY` como env var. Com OAuth, usar `claude -p` sem `--bare`.

**Quando usar cada abordagem de loop externo:**

| Objetivo | Abordagem |
|---|---|
| Tarefa recorrente, horário fixo | cron + `claude -p` |
| Loop com contexto preservado entre execuções | cron + `claude -p --resume <session_id>` |
| Loop condicional ("até X acontecer") | shell script `while` + `claude -p` |
| Loop orientado a evento | `inotifywait` / systemd `.path` + `claude -p` |
| Tarefa sem bloquear sessão atual | subagente `background: true` em `.claude/agents/` |
| Pipeline com hooks programáticos | Agent SDK Python |

**Boas práticas validadas:**
- Circuit breaker explícito: `MAX` de iterações no script ou `maxTurns: N` no frontmatter do subagente
- Estado em arquivo — entre invocações o Claude começa sem contexto
- Prompt autocontido — sem histórico de conversa, incluir todo o contexto necessário
- `--allowedTools` — restringir ao mínimo necessário em automações
- Logging: `>> /var/log/claude-cron.log 2>&1`
- Idempotência: verificar se a operação pode rodar N vezes sem acúmulo de efeito colateral

---

## Gaps — o que ainda precisamos entender

- [ ] Como o OpenClaw implementa o padrão maker/checker na prática (sem acesso ao código-fonte)
- [ ] Como o Hermes poderia implementar idempotency key sem redesenhar a API
- [ ] O padrão `/goal` do Claude Code — como funciona o checker de condição de parada
- [ ] Leitura pendente: os artigos do Addy Osmani abaixo (ainda não lidos/resumidos)

---

## Leituras pendentes (não consumidas ainda)

- **Agent Harness Engineering:** https://addyosmani.com/blog/agent-harness-engineering/
- **Long-Running Agents:** https://addyosmani.com/blog/long-running-agents/
- **The Orchestration Tax:** https://addyosmani.com/blog/orchestration-tax/
- **Adversarial Code Review (maker/checker):** https://addyosmani.com/blog/adversarial-code-review/
- **The Intent Debt:** https://addyosmani.com/blog/intent-debt/

---

## Conexões

- [[infraestrutura/hermes.md|Hermes]] — identidade, regras e stack do Hermes Agent
- [[pendencias/proximos-passos.md|Próximos Passos]] — to-do list ativa
