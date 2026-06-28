---
type: procedure
tags: [hermes, wiki, automacao, background, diario, plugin]
title: Wiki Review — Auto-escrita de diário em background
description: Plugin que roda a cada N turnos e salva insights da conversa na wiki (diario/).
timestamp: 2026-06-24T09:00:00-03:00
status: stable
---

# Wiki Review

Plugin do Hermes que analisa a conversa automaticamente e escreve insights no diário da wiki (`wiki/diario/YYYY-MM-DD-{session}.md`). Roda em background sem intervenção do usuário.

Nasceu como clone do `background_review.py` nativo do Hermes — mesma lógica de gatilho, mesmo padrão de spawn de thread e AIAgent filho — adaptado para escrever na wiki em vez de na `memory()`. A seção de comparação ao final deste arquivo documenta todas as divergências entre os dois.

## Implementação atual (v6 — sessão por session_id, contador por sessão)

**O que mudou em v6 (2026-06-24):**
- Estado unificado em `~/.hermes/wiki_review_session.json` como dicionário `{session_id: {diary_path, last_activity, counter}}`
- Contador deixou de ser global (`wiki_review_counter`) — agora é por session_id
- Arquivo de diário nomeado `YYYY-MM-DD-HHMM-{session_short}.md` — cada tópico/chat tem o seu
- Timestamps em BRT (`-03:00`) em vez de UTC
- Notificação Telegram no tópico wiki_review (thread 749) após cada commit

**Rollback para v5:** restaurar o `__init__.py` com `_get_diary_path()` sem parâmetro, SESSION_STATE_FILE como `{"diary_path":..., "last_activity":...}` e contador global em `~/.hermes/wiki_review_counter`. Ver seção v5 no histórico abaixo.

---

## Implementação anterior (v4/v5 — plugin, sobrevive a hermes update)

O wiki_review vive em `~/.hermes/plugins/wiki-review/`. Arquivo não rastreado pelo git do hermes-agent — sobrevive a qualquer `hermes update`.

```
~/.hermes/plugins/wiki-review/
  __init__.py    → lógica: contador, spawn de thread, AIAgent, git commit
  plugin.yaml    → manifest: name, version, hooks: [post_llm_call]
```

Ativação em `~/.hermes/config.yaml`:
```yaml
plugins:
  enabled:
    - wiki-review
```

### Fluxo

```
turno N → post_llm_call hook disparado
               ↓ verifica contador (/root/.hermes/wiki_review_counter)
               ↓ se count >= nudge_interval → reseta para 0, dispara
          spawn thread daemon
               ↓
          Python cria diary_path com frontmatter (se não existir)
               ↓
          AIAgent(enabled_toolsets=["file"], skip_memory=True)
          → run_conversation(prompt, histórico)
          → write_file(/tmp/wiki_review_{session}.md)   ← APENAS novas seções
               ↓ após agente terminar
          Python lê temp, faz append no diary_path, deleta temp
               ↓
          git -C /root/wiki add -A && git commit
               ↓ hook post-commit
          git pull --rebase + push
```

### Nome do arquivo de saída

```
wiki/diario/YYYY-MM-DD-HHMM.md
```

O horário é o do **primeiro disparo da sessão**. Disparos subsequentes na mesma sessão appendam no mesmo arquivo.

**Por que não usa session_id?** No gateway Telegram o `session_id` é o `chat_id` — fixo para sempre, igual em todas as conversas do usuário. Usar o session_id como nome faria TODAS as conversas ir para o mesmo arquivo. A detecção de sessão por inatividade resolve isso sem precisar de acesso ao objeto `agent`.

### Detecção de sessão por inatividade

Estado persistido em `/root/.hermes/wiki_review_session.json`:
```json
{"diary_path": "/root/wiki/wiki/diario/2026-06-24-1430.md", "last_activity": "2026-06-24T14:32:00+00:00"}
```

Lógica:
- Se `now - last_activity < 60 min` → mesma sessão, usa o mesmo arquivo, atualiza `last_activity`
- Se `now - last_activity >= 60 min` → nova sessão, cria arquivo com novo `HHMM`, reseta o estado

### Por que arquivo temporário?

O bug crítico das versões anteriores: o agente tentava ler o diário existente e reescrever o arquivo inteiro com as novas seções adicionadas. O argumento `content` do `write_file` ficava enorme (frontmatter + conteúdo antigo + novas seções). O modelo truncava o JSON na geração e o `message_sanitization` não conseguia reparar:

```
WARNING agent.message_sanitization: Unrepairable tool_call arguments for write_file
```

A solução: Python cria o arquivo e faz o merge. O agente só gera as novas seções (~10-20 linhas) e salva num temp. JSON pequeno, sem truncamento.

### Parâmetros

| Parâmetro | Onde | Padrão |
|---|---|---|
| Intervalo de disparo | `memory.nudge_interval` em config.yaml | 10 |
| Diretório da wiki | hardcoded no plugin | `/root/wiki` |
| Modelo usado | passado pelo hook `post_llm_call` | mesmo do turno |

### Contador de turnos

Persiste em `/root/.hermes/wiki_review_counter` (inteiro simples). Sobrevive a restarts do gateway. Incrementa a cada turno; zera quando dispara.

### Comparação completa com background_review (41 itens)

Documentação de todas as diferenças identificadas entre `wiki_review.py` (clone customizado) e `background_review.py` (nativo do Hermes). Gerada em 2026-06-24 após revisão linha a linha dos dois arquivos.

**Legenda:**
- ✅ — correto/igual/corrigido
- ❌ pendente — falta, pode ser problema ou melhoria futura
- ⚠️ OK — diferença intencional ou sem impacto real
- 🆕 único — existe só no wiki_review, correto assim

| # | Categoria | Item | background_review | wiki_review | Status |
|---|---|---|---|---|---|
| 1 | Callback | `_bg_review_auto_deny` (terminal auto-deny) | antes do try | adicionado (2026-06-24) | ✅ corrigido |
| 2 | Callback | `_set_approval_callback(None)` no finally | sim | adicionado (2026-06-24) | ✅ corrigido |
| 3 | Agente | `review_agent = None` antes do try (init defensivo) | sim | não | ❌ pendente |
| 4 | Agente | `enabled_toolsets` no constructor | herda do pai | `["file"]` | ⚠️ intencional |
| 5 | Agente | `disabled_toolsets` no constructor | herda do pai | ausente | ⚠️ OK |
| 6 | Agente | `_memory_write_origin` | "background_review" | ausente | ⚠️ OK (skip_memory) |
| 7 | Agente | `_memory_write_context` | "background_review" | ausente | ⚠️ OK (skip_memory) |
| 8 | Agente | `_skip_mcp_refresh = True` | sim | adicionado (2026-06-24) | ✅ corrigido |
| 9 | Agente | `_memory_store = agent._memory_store` | sim | ausente | ⚠️ OK (skip_memory) |
| 10 | Agente | `_memory_enabled = agent._memory_enabled` | sim | ausente | ⚠️ OK (skip_memory) |
| 11 | Agente | `_user_profile_enabled = agent._user_profile_enabled` | sim | ausente | ⚠️ OK (skip_memory) |
| 12 | Agente | `_memory_nudge_interval = 0` | sim | sim | ✅ igual |
| 13 | Agente | `_skill_nudge_interval = 0` | sim | sim | ✅ igual |
| 14 | Agente | `suppress_status_output = True` | sim | sim | ✅ igual |
| 15 | Cache | `_cached_system_prompt` herdado do pai (mesmo modelo) | sim | não | ❌ pendente |
| 16 | Cache | `session_start` herdado do pai (junto com #15) | sim | não | ❌ pendente |
| 17 | Sessão | `session_id = agent.session_id` | sim | sim | ✅ igual |
| 18 | Sessão | `_end_session_on_close = False` | sim | sim | ✅ igual |
| 19 | Sessão | `compression_enabled = False` | sim | sim | ✅ igual |
| 20 | Whitelist | ferramentas permitidas | memory + skills | file | ⚠️ intencional |
| 21 | Whitelist | `clear_thread_tool_whitelist` no finally | sim | sim | ✅ igual |
| 22 | Histórico | digest compacto quando routed (modelo diferente) | `_digest_history()` | sempre full | ❌ pendente |
| 23 | Prompt | aviso inline "outras tools serão negadas" no run_conversation | sim | não | ❌ menor |
| 24 | Teardown | `shutdown_memory_provider()` dentro do redirect | sim | não | ⚠️ OK (skip_memory) |
| 25 | Teardown | `review_agent.close()` | sim | sim | ✅ igual |
| 26 | Teardown | `review_agent = None` após close | sim | não | ❌ menor |
| 27 | Mensagens | snapshot `_session_messages` após run | sim | não | ⚠️ intencional |
| 28 | Notificação | `_safe_print` com mensagem de conclusão | `💾 Self-improvement review: ...` | `📓 Wiki review: 📝 Wiki daily note atualizada` | ✅ restaurado (2026-06-24) |
| 29 | Notificação | `background_review_callback` | sim | adicionado (2026-06-24) | ✅ restaurado |
| 30 | Erro | `logger.warning` no except da thread | sim | adicionado (2026-06-24) | ✅ corrigido |
| 31 | Erro | `agent._emit_auxiliary_failure` no except | sim | não | ⚠️ OK (silencioso por design) |
| 32 | Erro | cleanup de `review_agent` no finally (exception path) | sim | não | ❌ menor |
| 33 | Único wiki | git commit após escrever o diário | — | sim | 🆕 único |
| 34 | Único wiki | frontmatter YAML no arquivo de diário | — | sim | 🆕 único |
| 35 | Único wiki | contador persistido em disco (`wiki_review_counter`) | — | removido (2026-06-24) | 🆕 substituído |
| 36 | Ativação | Tipo de contador | RAM (zera no restart) | RAM (2026-06-24) | ✅ corrigido |
| 37 | Ativação | O que incrementa o contador | turno com `memory` disponível + `_memory_store` truthy | idem (2026-06-24) | ✅ corrigido |
| 38 | Ativação | Número de gatilhos | 2 independentes (memória + skills) | 1 (só memória) | ⚠️ intencional |
| 39 | Ativação | Condição extra para disparar | memory tool disponível + `_memory_store` truthy | idem (2026-06-24) | ✅ corrigido |
| 40 | Ativação | Intervalo padrão | `_memory_nudge_interval` (padrão 10 turnos) | idem (2026-06-24) | ✅ corrigido |
| 41 | Ativação | Entry points no código | `turn_finalizer.py` + `codex_runtime.py` | só `turn_finalizer.py` | ⚠️ OK |

**Itens ❌ pendentes por ordem de impacto:**

| # | Item | Impacto |
|---|---|---|
| 15+16 | cache parity (`_cached_system_prompt`, `session_start`) | tokens extras a cada disparo (custo) |
| 22 | digest logic para modelo roteado | necessário ao configurar modelo auxiliar mais barato |
| 3+26+32 | `review_agent = None` defensivo + cleanup no except/finally | leak se o constructor do AIAgent falhar no meio |
| 23 | aviso inline sobre tools no prompt | reduz tentativas do modelo de usar tools negadas |

**Histórico de correções (2026-06-24):**

| Item | O que foi corrigido |
|---|---|
| Gatilho (#36–40) | Trocado contador em disco por `_should_review_memory` (mesmo gatilho do background_review) |
| `_skip_mcp_refresh` (#8) | Adicionado — evita que refresh de MCP corrompa o fork |
| `_bg_review_auto_deny` (#1) | Adicionado — evita deadlock em input() se terminal tool for acionada |
| `_set_approval_callback(None)` (#2) | Adicionado no finally — limpa callback ao fim da thread |
| Logger na thread (#30) | Trocado `logger.debug` por `logger.warning` — erros agora visíveis nos logs |
| `_safe_print` + callback (#28+29) | Restaurado — `📓 Wiki review: 📝 Wiki daily note atualizada` (existia na versão original, perdido na recriação do arquivo) |

## O prompt — critério de durabilidade

O agente filho recebe o histórico e filtra por **durabilidade**: só escreve o que for útil na PRÓXIMA sessão. Não é um resumo do que aconteceu.

Sinais que justificam uma entrada:
- Usuário corrigiu comportamento/tom/abordagem (frustração explícita é sinal de primeira classe)
- Usuário revelou algo DURÁVEL sobre si — preferência estável, estilo de trabalho, objetivo de longo prazo
- Técnica, fix, config ou caminho de debugging reutilizável surgiu
- Decisão explicitamente adiada com follow-up necessário

Não captura (ruído que polui o diário):
- Narrativas de sessão ("falamos sobre X", "testamos Y") — o que aconteceu hoje não é durável
- Erros transitórios que se resolveram
- Pendências vagas sem dono ou contexto acionável
- Afirmações negativas sobre ferramentas ("X não funciona")
- Resumos do que o agente pesquisou ou produziu

**"Nada a registrar." é um outcome legítimo e esperado** — sessões tranquilas sem correções devem retornar vazio.

Cada finding vira uma seção `## {título} — {HH:MM}` com 2-4 linhas: o que é, por que importa para o futuro, como aplicar.

## Arquivo de saída

Ver seção "Nome do arquivo de saída" acima — gerado por detecção de sessão por inatividade.

## Restrições do agente filho

- Toolset: apenas `file` (read_file, write_file) + whitelist de thread enforced em runtime
- `skip_memory=True` — não acessa nem escreve na `memory()` do Hermes
- `max_iterations=16`
- `compression_enabled=False`
- Auto-deny em comandos perigosos (mesma proteção do background_review)

## Rollback

```bash
# Desativar plugin
# Editar ~/.hermes/config.yaml: remover "- wiki-review" de plugins.enabled
hermes gateway restart

# Remover completamente
rm -rf /root/.hermes/plugins/wiki-review/
```

---

## Histórico de implementações (para não repetir erros)

### v1 — source tree (2026-06-22, funcionou até o próximo update)

Código adicionado diretamente em `/usr/local/lib/hermes-agent/agent/turn_finalizer.py` e `agent/wiki_review.py` no source tree do Hermes. Funcionou inicialmente.

**Por que falhou:** o source tree em `/usr/local/lib/hermes-agent/` é o git repo do hermes-agent. `hermes update` faz `git pull` — qualquer mudança no `turn_finalizer.py` upstream gerava conflito e o git fazia stash das modificações locais. As mudanças ficaram presas em `git stash@{0}` e foram perdidas.

---

### v2 — .hermes/agent/ override com turn_finalizer (2026-06-23, quebrava em todo update)

Gatilho adicionado ao override `/root/.hermes/agent/turn_finalizer.py` (que é o arquivo realmente carregado — ver editable install). `wiki_review.py` em `/root/.hermes/agent/wiki_review.py` (não rastreado, sobrevive).

**Por que falhou:** `turn_finalizer.py` É rastreado pelo git (`git status` mostra `M agent/turn_finalizer.py`). Em todo `hermes update` onde o upstream muda esse arquivo, o git cria conflito e faz stash das mudanças locais. Como `turn_finalizer.py` é um arquivo central que muda frequentemente no upstream, na prática quebrava em todo update.

**Lição:** arquivos rastreados pelo git do hermes-agent nunca são lugar seguro para customizações que precisam sobreviver a updates.

---

### v3 — plugin com estratégia errada de escrita (2026-06-24, não funcionou)

Plugin correto em `~/.hermes/plugins/wiki-review/` (não rastreado, sobrevive a updates). Mas o prompt pedia para o agente **ler o arquivo existente e reescrever tudo de volta** com as novas seções.

**Por que falhou:** o argumento `content` do `write_file` ficava enorme (frontmatter + conteúdo antigo + novas seções). O modelo truncava o JSON no meio da geração. O `message_sanitization` registrava:
```
WARNING: Unrepairable tool_call arguments for write_file — replaced with empty object
```
Resultado: o arquivo nunca era escrito. Aconteceu 10+ vezes nos logs.

**Causa raiz:** contextos pesados (sessões longas com DeepSeek em ~97k tokens) agravavam o problema — stream stale de 311s foi registrado.

---

### v4 — plugin com arquivo temporário (2026-06-24, superado por v5)

Plugin não rastreado + temp file. Corrigiu o bug do write_file. Mas usava `session_id[:8]` para nomear o arquivo — e o session_id no gateway Telegram é o chat_id (fixo), então todos os disparos do dia iam para o mesmo arquivo. Um arquivo acumulou +10 sessões misturadas.

---

### v5 — detecção de sessão por inatividade (2026-06-24, implementação atual)

Plugin não rastreado + estratégia de escrita correta: Python cria o arquivo com frontmatter, agente gera só as novas seções em `/tmp/`, Python faz o append. Sem leitura nem reescrita do arquivo inteiro pelo agente.

Um arquivo por sessão de conversa. Sessão detectada por inatividade (60 min sem turno = nova sessão). Nome do arquivo: `YYYY-MM-DD-HHMM.md`. Estado persistido em `~/.hermes/wiki_review_session.json`.

## Conexões

- [[infraestrutura/hermes.md|Hermes Config]] — stack e arquitetura
- [[conhecimento/wiki.md|Wiki]] — estrutura da wiki e regras de escrita
- [[pendencias/proximos-passos.md|Próximos passos]]
