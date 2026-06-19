---
tags:
- hermes
- rule
- workflow
tier: semantic
---
# Regra: Sempre carregar e seguir a skill antes de executar

**Data:** 2026-06-18

## O problema
O agente ignorou a skill `alexa-notifications` que já tinha o comando exato documentado. Em vez de usar a skill, saiu debugando: testou 50 variações de curl, mexeu em config do Node-RED, quis alterar a skill. O comando correto já estava na skill e funcionava.

## A regra
1. **Antes de qualquer ação, carregar a skill relevante** com `skill_view(nome)`
2. **Se a skill descreve o comando, usar ele exatamente como está** — não adaptar, não "melhorar", não testar alternativas
3. **Se o comando falhar**, aí sim investigar — mas partindo da skill como fonte da verdade
4. **Nunca modificar uma skill sem autorização explícita do usuário**

## Consequências de ignorar a regra
- Desperdício de tempo do usuário (teve que aturar dezenas de comandos desnecessários)
- Risco de desconfigurar algo que funcionava
- Usuário perde confiança no agente

## Referência
- Skill: `alexa-notifications` — sempre conferir antes de agir
- Lição aprendida na sessão de 2026-06-18
