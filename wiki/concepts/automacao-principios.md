---
type: concept
tags: [automacao, testes, validacao, evals, karpathy, principios, agentes, observabilidade]
title: Princípios de Automação e Testes
description: Regras gerais para construção, validação e maturação de automações multi-agente — derivadas do postmortem do auditor v1, do framework Karpathy e das lições do ciclo de evals.
timestamp: 2026-06-29T00:00:00-03:00
status: draft
---

# Princípios de Automação e Testes

Documento de referência geral. Aplica-se a qualquer automação construída neste setup — não é específico do auditor, do curador ou de qualquer outro script. Cada princípio aqui foi destilado de uma falha real ou de uma lição aprendida com custo.

---

## Framework central: spec design → eval loops → deploy

Derivado de Karpathy (via Akshay Pachaar). A sequência não é sugestão — é gate. Você não vai para o próximo estágio sem ter passado pelo anterior.

```
spec design → eval loops → deploy
```

**Spec design:** o que o sistema deve fazer, com que inputs, com que outputs, com que contratos de interface. Sem spec, não há como saber se o eval passou ou falhou.

**Eval loops:** execuções controladas que provam que o sistema faz o que a spec diz. Cada eval é um gate bloqueante — não é opcional, não é "rodar quando der".

**Deploy:** só depois que os evals passaram. Nunca antes.

> "89% das equipes mantêm observabilidade, mas apenas 52% implementam frameworks de avaliação." O auditor v1 foi para produção nessa outra metade.

---

## Antes de implementar

**Escreva o contrato antes de escrever o código.**
O contrato é: dado este input, espero este output, com estes campos, neste formato. Se você não consegue descrever o output esperado antes de rodar, não está pronto para implementar.

**Não existe "vou ver o que sai".**
Se o output esperado é desconhecido antes do run, o que você está fazendo é exploração, não validação. Exploração tem valor, mas não é um eval.

---

## Os princípios

### 1. Sintético antes de real

Qualquer teste que invoca agentes usa dados sintéticos antes de dados reais. Dados sintéticos significam:
- Conteúdo mínimo, passado inline no prompt (zero reads, custo perto de zero)
- Problema conhecido e plantado (você sabe o que o agente deveria encontrar)
- Output esperado definido antes do run

Dados reais só entram quando o contrato já foi validado com sintético. Um arquivo real de 10KB não é "o menor possível" — é um custo desnecessário num teste de contrato.

### 2. Fail-fast, não silencioso

Se um componente produz output inválido, o sistema deve parar imediatamente e alertar — não continuar com dados vazios.

O que o auditor v1 fez: quando um agente produziu prosa em vez de JSON, o fallback `findings: []` aceitou silenciosamente e seguiu. O erro estava invisível até o final, depois que 75% da cota de tokens foi gasta.

O que o sistema deve fazer: detectar na hora, logar os primeiros 300 chars do output inválido, parar.

```python
try:
    result = json.loads(agent_output)
except json.JSONDecodeError:
    print("ERRO: output inválido (primeiros 300 chars):", agent_output[:300])
    raise  # nunca engolir silenciosamente
```

### 3. Serial antes de paralelo

Provar que **um** componente funciona → provar que **dois** funcionam → paralelizar. Nunca lançar N instâncias com código não validado.

O auditor v1 lançou 8 agentes simultâneos na primeira execução real. Quando todos falharam, foram 8× o custo de uma única falha — e as falhas se mascararam mutuamente.

Sequência correta:
1. Um agente, dados sintéticos
2. Um agente, dados reais
3. Dois agentes, dados reais
4. Todos os agentes

### 4. Prompt-as-contract

Um system prompt completo inclui obrigatoriamente:
- Instrução de formato (ex: "responda apenas em JSON")
- Exemplo concreto do output esperado, com todos os campos
- Instrução explícita para o caso vazio (ex: `{"findings": []}`)
- Proibição de prosa ("nenhuma palavra fora do JSON")

O flag `--output-format json` (ou equivalente) é complemento, não substituto. O modelo pode ignorar o flag se o system prompt não for explícito. O contrato está no prompt, não no parâmetro da API.

### 5. Custo como critério de reprovação, não como estimativa

"Custo esperado: < 30KB de contexto" não é um gate — é wishful thinking. Um critério real tem formato:

> Se `input_tokens > 20K`, o eval **falhou** — parar e investigar antes de prosseguir.

Se ultrapassar o limite não reprova o eval, o limite não existe. Aspirações não protegem contra gastos inesperados.

### 6. Observabilidade embutida no output

O agente deve reportar o que fez, não apenas o que encontrou. O campo `_meta` é o mecanismo padrão:

```json
"_meta": {
  "files_read": ["AGENTS.md", "systems/hermes.md"],
  "read_calls": 2,
  "approx_chars_read": 18400,
  "limit_reached": false
}
```

Sem `_meta`, você não sabe se o agente leu 2 arquivos ou 20. Não sabe se o limite foi atingido. Não sabe por que o custo foi alto. Observabilidade embutida no output é mais confiável do que logs externos — o agente sabe o que fez, então é ele que reporta.

### 7. Limite de ações como soft timeout

Todo agente que executa ações repetitivas (reads, chamadas de API, iterações) deve ter um limite explícito no prompt:

> "Faça no máximo N Read calls. Se atingir esse limite, retorne o que coletou até aqui e defina `_meta.limit_reached: true`."

Sem limite, um agente pode trabalhar indefinidamente — consumindo tokens, tempo e cota. O limite não é uma punição, é um contrato: "se você não terminou em N calls, algo está errado e eu preciso saber."

### 8. O orquestrador não confia no silêncio

O componente orquestrador (seja bash, Python ou a sessão principal do Claude Code) não pode presumir que ausência de erro significa sucesso. Ele deve verificar ativamente:
- O output tem o formato esperado?
- Os campos obrigatórios estão presentes?
- O `_meta` reporta valores plausíveis?

Silêncio não é confirmação. Confirmação é verificação explícita.

---

## Mecanismo de observabilidade: JSONL de sessão

O Claude Code salva cada turno de sessão em `~/.claude/projects/<projeto>/*.jsonl`. Cada turno assistente contém:

```json
{
  "usage": {
    "input_tokens": 3,
    "cache_creation_input_tokens": 8945,
    "cache_read_input_tokens": 12584,
    "output_tokens": 185
  }
}
```

Para medir o custo real de uma operação: ler o JSONL antes e depois, calcular o delta. Isso dá:
- Tokens de entrada novos (pagos integralmente)
- Tokens lidos do cache (mais baratos)
- Tokens de saída gerados
- Número de turnos (= chamadas à API)

Não confiar em estimativas — medir sempre com JSONL delta.

---

## Ciclo de maturidade de uma automação

```
ideia → spec → eval sintético → eval real isolado → eval serial → paralelo → dry-run → deploy
```

Cada estágio é um gate. A maturidade não se declara — se prova passando pelos evals na ordem.

| Estágio | O que prova | Dados |
|---|---|---|
| Spec | O contrato está claro | nenhum |
| Eval sintético | O agente obedece o contrato | sintéticos, inline |
| Eval real isolado | O contrato sobrevive a dados reais | 1 arquivo real |
| Eval serial | Todos os componentes funcionam individualmente | dados reais, um por vez |
| Paralelo | A escala não quebra o contrato | dados reais, paralelo |
| Dry-run | O fluxo completo funciona sem efeitos colaterais | produção, read-only |
| Deploy | Autorização explícita do usuário | produção, com efeitos |

---

## Lições do auditor v1 (postmortem resumido)

O auditor v1 foi para produção sem nenhum eval. Resultado: 75% da cota de tokens consumida em 5 minutos, zero findings entregues.

Causas raiz que este documento endereça:

| Causa (v1) | Princípio correspondente |
|---|---|
| 8 agentes paralelos na primeira execução | Serial antes de paralelo (#3) |
| Fallback silencioso `findings: []` | Fail-fast, não silencioso (#2) |
| Nenhum agente testado isoladamente antes | Sintético antes de real (#1) |
| `--output-format json` como única garantia | Prompt-as-contract (#4) |
| "Custo esperado" sem critério de parada | Custo como critério (#5) |
| Sem forma de saber o que cada agente fez | Observabilidade embutida (#6) |
| Sem limite de calls por agente | Soft timeout (#7) |
| Coordenador aceitou input vazio e seguiu | Orquestrador não confia no silêncio (#8) |

---

## Conexões

- [[wiki/procedures/auditor-wiki-evals.md|Auditor da Wiki — Evals e Validação]] — aplicação destes princípios em contexto concreto
- [[wiki/procedures/auditor-wiki.md|Auditor da Wiki]] — postmortem completo da execução v1
- [[wiki/concepts/orquestrador.md|Orquestrador da Memória]] — automação que deve seguir estes princípios na próxima versão
