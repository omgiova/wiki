---
type: concept
tags: [agentes, loops, arquitetura, loop-engineering, planejamento]
title: Plano — Loop Engineering
description: Plano centralizado para entender, testar e aplicar loops agênticos. Agnóstico de modelo e ambiente. Inclui referências, testes e o que já foi validado.
timestamp: 2026-06-27T02:10:00-03:00
status: draft
---

# Plano — Loop Engineering

## Tese

> *"You shouldn't be prompting coding agents anymore. You should be designing loops that prompt your agents."*
> — Peter Steinberger (@steipete), criador do OpenClaw
> Fonte: https://x.com/steipete/status/2063697162748260627

> *"I don't prompt Claude anymore. I have loops running that prompt Claude and figuring out what to do. My job is to write loops."*
> — Boris Cherny, head of Claude Code na Anthropic
> Fonte: https://www.youtube.com/watch?v=SlGRN8jh2RI

O ponto de alavancagem mudou: não é mais escrever o prompt certo — é escrever o sistema que dispara os prompts.

---

## Princípios (agnósticos de modelo e ambiente)

Baseado em: Addy Osmani, *"Loop Engineering"* — https://addyosmani.com/blog/loop-engineering/

> *"Replacing yourself as the person who prompts the agent. You design the system that does it instead."*

**Os 6 componentes de um loop:**

| # | Componente | O que é |
|---|---|---|
| 1 | **Automations** | o que dispara o loop (cron, evento, condição) |
| 2 | **Worktrees** | ambiente isolado onde o agente opera |
| 3 | **Skills** | capacidades específicas disponíveis ao agente |
| 4 | **Plugins/connectors** | integrações com sistemas externos |
| 5 | **Subagents** | agentes especializados para tarefas específicas |
| 6 | **Memory/state** | o que persiste entre execuções |

**Princípios fundamentais:**

- **Maker/checker** — o modelo que produziu algo não deve revisar o próprio trabalho. Um segundo agente com instruções diferentes (e às vezes modelo diferente) pega o que o primeiro se convenceu a ignorar.
- **Memória em disco, não em contexto** — o agente esquece tudo entre execuções. O estado fica em arquivo, não no histórico.
- **Condição de parada verificada por agente separado** — maker e checker são distintos, inclusive para decidir quando parar.
- **Loop sem supervisão = erro sem supervisão** — autonomia exige circuit breaker explícito.

---

## O que já foi validado ✅

### Loop externo com Claude Code CLI na VPS

> Testado em 2026-06-27 (Claude Code v2.1.183, autenticação OAuth).

O binário `claude` pode ser acionado pelo cron do sistema sem sessão interativa aberta. Credenciais OAuth ficam em `~/.claude/.credentials.json` e são lidas automaticamente.

| Comando | Ambiente mínimo (env -i) | Resultado |
|---|---|---|
| `claude --bare -p "..."` | HOME=/root PATH=... | ❌ "Not logged in" |
| `claude -p "..."` | HOME=/root PATH=... | ✅ funciona |

`--bare` é incompatível com OAuth — assume `ANTHROPIC_API_KEY`. Com OAuth, usar `claude -p` sem `--bare`.

**Modalidades de loop externo:**

| Objetivo | Abordagem |
|---|---|
| Tarefa recorrente, horário fixo | cron + `claude -p` |
| Loop com contexto preservado | cron + `claude -p --resume <session_id>` |
| Loop condicional ("até X acontecer") | shell script `while` + `claude -p` |
| Loop orientado a evento | `inotifywait` / systemd `.path` + `claude -p` |
| Tarefa sem bloquear sessão atual | subagente `background: true` em `.claude/agents/` |
| Pipeline com hooks programáticos | Agent SDK Python |

**Boas práticas validadas:**
- Circuit breaker explícito: `MAX` iterações no script ou `maxTurns: N` no frontmatter do subagente
- Estado em arquivo — prompt autocontido, sem histórico de conversa
- `--allowedTools` — restringir ao mínimo necessário
- Logging: `>> /var/log/claude-cron.log 2>&1`
- Idempotência: operação deve poder rodar N vezes sem efeito cumulativo

---

## Sistemas para testar

### Teste 1: karpathy/autoresearch

**Repositório:** https://github.com/karpathy/autoresearch  
**Autor:** Andrej Karpathy  
**Stars:** 88.8k  
**Stack:** Python + uv  
**Status:** ativo (último commit mar/2026)

**O que é:**  
Loop de pesquisa autônoma em ML. O agente modifica `train.py`, treina por 5 minutos, verifica se o resultado melhorou, mantém ou descarta, e repete indefinidamente. O humano não toca no código — edita o `program.md` (as instruções do agente) e vai dormir.

**Arquitetura do loop:**

```
program.md          → skill/contexto do agente (editado pelo humano)
train.py            → único arquivo que o agente modifica
prepare.py          → imutável (avaliação, dados, tokenizer)
results.tsv         → memória em disco, não commitada, não vai pro contexto
```

**Lógica de decisão via git:**
```
val_bpb melhorou? → git commit (keep — avança o branch)
val_bpb piorou?   → git reset  (discard — volta ao estado anterior)
```
O git não é só versionamento — é a máquina de estados do loop.

**Supressão de output do contexto (padrão explícito):**
```bash
uv run train.py > run.log 2>&1        # output nunca vai pro contexto
grep "^val_bpb:\|^peak_vram_mb:" run.log  # só o sinal, sem ruído
```

**Condição de parada:**  
Externa — o humano interrompe. O agente não pergunta se deve continuar. `program.md` instrui explicitamente: *"NEVER STOP. Do NOT pause to ask the human."*

**Critério de sucesso do experimento (simplicity criterion):**  
Não é só "o score melhorou?" — é "o ganho justifica a complexidade adicionada?":
- Ganho de 0.001 + 20 linhas novas → discard
- Remoção de código com ganho igual → keep
- Ganho zero + código mais simples → keep

**Pré-requisitos:**
- GPU NVIDIA (testado em H100) — ⚠️ **VPS atual não tem GPU NVIDIA**
- Python 3.10+, uv
- Forks disponíveis para MacOS MLX e outros ambientes

**Opções para rodar:**
- Máquina com GPU NVIDIA
- Instância cloud com GPU (Vast.ai, RunPod)
- Fork para outro hardware
- Adaptar o loop para tarefa sem GPU (substituir `train.py` por outra tarefa mensurável)

---

### Teste 2: OpenClaw

**Repositório:** https://github.com/openclaw/openclaw  
**Autor:** Peter Steinberger + comunidade  
**Stars:** 381k  
**Stack:** TypeScript/Node.js  
**Status:** ativo (último commit 27 jun 2026)

> *"OpenClaw treats AI as an infrastructure problem: sessions, memory, tool sandboxing, access control, and orchestration. The LLM provides intelligence; OpenClaw provides the execution environment."*  
> — Paolo Perazzo, https://ppaolo.substack.com/p/openclaw-system-architecture-overview

**O que é:**  
Loop agêntico avançado com gateway central em WebSocket (`127.0.0.1:18789`). Todos os canais (WhatsApp, Telegram, Discord, CLI, Web UI) conectam no mesmo control plane.

**Arquitetura (hub-and-spoke):**

Agent Runtime (`src/agents/piembeddedrunner.ts`) — a cada turno:
1. Resolve sessão
2. Monta contexto
3. Streama resposta e executa tool calls
4. Persiste estado

**Decisões de design relevantes:**
- System prompt como **stack de arquivos** — composição de múltiplas configs, não string única
- **Idempotency key obrigatório** em toda operação com efeito colateral — retry seguro sem duplicação
- **Cron jobs escopados por agente** — cada agente tem os seus próprios (commit #96883)
- **Session isolation** — sessão `main` roda tools no host; `dm`/`group` têm sandbox restrito
- **Plugin system** — 4 tipos (channel, memory, tool, provider), discovery via `package.json`

**Sistema de detecção de loop infinito (issue #57263):**

| Detector | O que detecta | Warning | Bloqueio |
|---|---|---|---|
| `generic_repeat` | mesma tool + args + resultado idêntico | 10 calls | 20 calls |
| `unknown_tool_repeat` | tool inexistente chamada repetidamente | — | 10 calls |
| `known_poll_no_progress` | poll/log sem progresso real | 10 calls | 20 calls |
| `global_circuit_breaker` | qualquer tool 30+ vezes sem progresso | — | 30 calls (hard block) |
| `ping_pong` | alternância A→B→A→B sem resultado novo | 10 calls | 20 calls |

**Tool Outcome Hashing:** `sha256(args_sem_volatile + resultado)` — campos voláteis (`timestamp`, `messageId`, `runId`) removidos antes do hash. Só é loop se o **resultado também não mudou**.

Arquivos relevantes (não acessíveis sem autenticação GitHub):
- `src/agents/tool-loop-detection.ts`
- `src/agents/embedded-agent-runner/run.ts`
- `src/agents/post-compaction-loop-guard.ts`

---

## Gaps — o que ainda precisamos entender

- [ ] Como o OpenClaw implementa maker/checker na prática (sem acesso ao código-fonte)
- [ ] O padrão `/goal` do Claude Code — como funciona o checker de condição de parada
- [ ] Como implementar idempotency key sem redesenhar APIs existentes

---

## Leituras pendentes

- **Agent Harness Engineering:** https://addyosmani.com/blog/agent-harness-engineering/
- **Long-Running Agents:** https://addyosmani.com/blog/long-running-agents/
- **The Orchestration Tax:** https://addyosmani.com/blog/orchestration-tax/
- **Adversarial Code Review (maker/checker):** https://addyosmani.com/blog/adversarial-code-review/
- **The Intent Debt:** https://addyosmani.com/blog/intent-debt/

---

## Conexões

- [[infraestrutura/hermes.md|Hermes]] — identidade, regras e stack do Hermes Agent
- [[pendencias/proximos-passos.md|Próximos Passos]] — to-do list ativa
