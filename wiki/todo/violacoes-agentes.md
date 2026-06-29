---
type: todo
tags: [violacoes, agentes, regras, log]
title: Violações de Regras — Agentes
description: Registro de violações graves cometidas por agentes às regras explícitas do AGENTS.md; serve de memória para evitar reincidência.
timestamp: 2026-06-29T08:35:41-03:00
status: draft
---

# Violações de Regras — Agentes

Registro append-only de violações graves de regras explícitas do [[AGENTS.md]]. Cada item descreve o que foi feito, qual regra foi quebrada e o que deveria ter acontecido.

---

## V1 — Edição de entrada existente no log.md

**Data:** 2026-06-29  
**Regra violada:** `log.md` é append-only — "Nunca editar entradas existentes" (AGENTS.md)  
**O que aconteceu:** ao detectar uma entrada malformada no log.md (variável `$(date)` não expandida na linha 326), o agente editou diretamente o arquivo para remover a linha, alterando o histórico existente.  
**O que deveria ter feito:** appendar uma linha de correção logo após a entrada malformada, sinalizando o problema sem alterar o que já estava escrito.  
**Impacto:** histórico do log alterado; entrada original perdida do arquivo (ainda recuperável via git).

---

---

## V2 — Edição de entrada existente no log.md (reincidência)

**Data:** 2026-06-29  
**Regra violada:** `log.md` é append-only — "Nunca editar entradas existentes" (AGENTS.md)  
**O que aconteceu:** ao documentar o Eval 2-B (3ª execução), o agente substituiu a entrada existente "APROVADO" pela entrada "REPROVADO" em vez de apenas appendaruma nova entrada corrigindo o status. A instrução "não leia wiki nem outros arquivos" impedia que o agente lesse o AGENTS.md com as regras.  
**O que deveria ter feito:** manter a entrada APROVADO intacta e appendar nova entrada abaixo explicando a correção de status.  
**Impacto:** entrada original "APROVADO" perdida do log (recuperável via git).

**Mitigação aplicada:** hook PreToolUse configurado em `/root/.claude/settings.json` que bloqueia automaticamente qualquer Edit ou Write no log.md que modifique conteúdo existente em vez de apenas appendar. Script em `/root/.claude/hooks/log-guard.py`.

---

## ⏳ PENDENTE — Validar eficácia do hook log-guard

**Data de configuração:** 2026-06-29  
**O que validar:** verificar nas próximas sessões se o hook PreToolUse (`log-guard.py`) bloqueia corretamente tentativas de edição de entradas existentes no log.md.  
**Critérios de validação:**
- [ ] Hook dispara ao tentar editar entrada existente (mensagem de bloqueio visível)
- [ ] Hook permite append legítimo (adicionar ao final sem alterar conteúdo anterior)
- [ ] Hook não interfere com edições de outros arquivos .md
- [ ] Comportamento consistente em sessões onde AGENTS.md não é lido

**Onde está:** `/root/.claude/settings.json` (hook global) + `/root/.claude/hooks/log-guard.py`  
**Limitação conhecida:** o hook só protege sessões Claude Code — outros processos na VPS não são cobertos.

---

## Conexões

- [[AGENTS.md]] — fonte das regras violadas
- [[log.md]] — arquivo afetado no V1 e V2
