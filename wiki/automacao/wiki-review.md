---
type: procedure
tags: [hermes, wiki, automacao, background, diario, plugin]
title: Wiki Review — Auto-escrita de diário em background
description: Plugin que roda a cada N turnos e salva insights da conversa na wiki (diario/).
timestamp: 2026-06-24T10:30:00+00:00
status: stable
---

# Wiki Review

Plugin do Hermes que analisa a conversa automaticamente e escreve insights no diário da wiki (`wiki/diario/YYYY-MM-DD-{session}.md`). Roda em background sem intervenção do usuário.

## Implementação atual (plugin — sobrevive a hermes update)

O wiki_review é um **plugin standalone** em `~/.hermes/plugins/wiki-review/`. Ele usa o hook `post_llm_call` do sistema de plugins do Hermes, que é chamado ao fim de cada turno.

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

### Por que plugin e não turn_finalizer.py?

O Python carrega o source tree `/usr/local/lib/hermes-agent/` — nunca `/root/.hermes/agent/`. Qualquer arquivo em `.hermes/agent/` é ignorado pelo runtime. Além disso, `hermes update` restaura o source tree, apagando patches manuais. O sistema de plugins em `~/.hermes/plugins/` é o único ponto de extensão que sobrevive a updates.

### Fluxo

```
turno N → post_llm_call hook disparado
               ↓ verifica contador (/root/.hermes/wiki_review_counter)
               ↓ se count >= nudge_interval → reseta para 0
          spawn thread daemon
               ↓
          AIAgent(enabled_toolsets=["file"], skip_memory=True)
          → run_conversation(prompt, histórico)
          → write_file(diario/YYYY-MM-DD-{session}.md)
               ↓ após agente terminar
          git -C /root/wiki add -A && git commit
               ↓ hook post-commit
          git pull --rebase + push
```

### Parâmetros configuráveis

| Parâmetro | Onde | Padrão |
|---|---|---|
| Intervalo de disparo | `memory.nudge_interval` em config.yaml | 10 |
| Diretório da wiki | hardcoded no plugin | `/root/wiki` |
| Modelo usado | `model` kwarg do hook (mesmo modelo do turno) | — |

### Contador de turnos

Persiste em `/root/.hermes/wiki_review_counter` (inteiro simples). Sobrevive a restarts do gateway. Incrementa a cada turno; zera quando dispara.

## O prompt (3 perguntas)

O agente filho recebe o histórico da conversa e responde a 3 perguntas:

1. **O usuário revelou algo sobre si?** — persona, desejos, preferências, estilo de trabalho
2. **Teve correção, técnica nova, ferramenta instalada, skill desatualizada?** — mudanças de abordagem, novos workflows, configurações
3. **Tem pendência ou próximo passo?** — decisões adiadas, issues abertas, caminhos não explorados

Cada finding vira uma seção `## {título} — {HH:MM}` no diário do dia.

## Nome do arquivo de saída

```
wiki/diario/YYYY-MM-DD-{8 chars do session_id}.md
```

Exemplo: `wiki/diario/2026-06-24-20260624.md`

Se o arquivo já existir (outro disparo no mesmo dia), o agente lê o conteúdo atual e reescreve com as novas seções anexadas.

## Restrições do agente filho

- Toolset: apenas `file` (read_file, write_file)
- `skip_memory=True` — não acessa nem escreve na `memory()` do Hermes
- `max_iterations=16`
- `compression_enabled=False`

## Histórico de implementações

### v1 — source tree (2026-06-22, funcionou)
Código adicionado diretamente em `/usr/local/lib/hermes-agent/agent/turn_finalizer.py` e `agent/wiki_review.py`. Funcionou. Depois foi "movido" para `.hermes/agent/` como "override" — conceito errado.

### v2 — .hermes/agent/ override (2026-06-23, nunca funcionou)
Bugs:
1. `.hermes/agent/` não está em `sys.path` — Python nunca carrega dali
2. Import errado: `from agent.background_review import _resolve_review_runtime` — função está em `agent.curator`

### v3 — plugin (2026-06-24, implementação atual)
Plugin em `~/.hermes/plugins/wiki-review/`. Usa hook `post_llm_call`. Sobrevive a `hermes update`. Confirmado funcionando pelos logs:
```
INFO hermes_plugins.wiki_review: wiki_review: disparado (session=20260624)
```

## Rollback

```bash
rm -rf /root/.hermes/plugins/wiki-review/
# remover "- wiki-review" de plugins.enabled em ~/.hermes/config.yaml
hermes gateway restart
```

## Conexões

- [[infraestrutura/hermes.md|Hermes Config]] — stack e arquitetura
- [[conhecimento/wiki.md|Wiki]] — estrutura do vault e regras de escrita
- [[pendencias/proximos-passos.md|Próximos passos]]
