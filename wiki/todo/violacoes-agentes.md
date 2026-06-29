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

## Conexões

- [[AGENTS.md]] — fonte das regras violadas
- [[log.md]] — arquivo afetado no V1
