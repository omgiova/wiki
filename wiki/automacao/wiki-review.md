---
type: procedure
tags: [hermes, wiki, automacao, background, diario, plugin]
title: Wiki Review — Auto-escrita de diário em background
description: Plugin que roda a cada N turnos e salva insights da conversa na wiki (diario/).
timestamp: 2026-06-24T12:00:00+00:00
status: stable
---

# Wiki Review

Plugin do Hermes que analisa a conversa automaticamente e escreve insights no diário da wiki (`wiki/diario/YYYY-MM-DD-{session}.md`). Roda em background sem intervenção do usuário.

## Implementação atual (v4 — plugin, sobrevive a hermes update)

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

### Limitações conhecidas em relação ao background_review

O plugin recebe `session_id`, `model`, `platform` e `conversation_history` via hook, mas não tem acesso ao objeto `agent`. Por isso:

- **Credenciais**: usa as do config.yaml, não herda o pool de credenciais do pai
- **Cache de prompt**: não herda `_cached_system_prompt` — não aproveita o cache de prefixo
- **Notificação visual**: apenas `logger.info`, não aparece no terminal do usuário (futuro: explorar hook `on_session_start` ou similar)
- **Condição de disparo**: contador por N turnos (mesmo `nudge_interval`), não exatamente `_should_review_memory`

## O prompt (3 perguntas)

O agente filho recebe o histórico da conversa e responde a 3 perguntas:

1. **O usuário revelou algo sobre si?** — persona, desejos, preferências, estilo de trabalho
2. **Teve correção, técnica nova, ferramenta instalada, skill desatualizada?** — mudanças de abordagem, novos workflows, configurações
3. **Tem pendência ou próximo passo?** — decisões adiadas, issues abertas, caminhos não explorados

Cada finding vira uma seção `## {título} — {HH:MM}` escrita no arquivo temporário.

## Nome do arquivo de saída

```
wiki/diario/YYYY-MM-DD-{8 chars do session_id}.md
```

Exemplo: `wiki/diario/2026-06-24-20260624.md`

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

### v4 — plugin com arquivo temporário (2026-06-24, implementação atual)

Plugin não rastreado + estratégia de escrita correta: Python cria o arquivo com frontmatter, agente gera só as novas seções em `/tmp/`, Python faz o append. Sem leitura nem reescrita do arquivo inteiro pelo agente.

## Conexões

- [[infraestrutura/hermes.md|Hermes Config]] — stack e arquitetura
- [[conhecimento/wiki.md|Wiki]] — estrutura do vault e regras de escrita
- [[pendencias/proximos-passos.md|Próximos passos]]
