---
type: tool
tags: [llm, providers, agentes, api, modelos]
title: LLM Providers — Registro de APIs de modelo
description: Registro central dos providers de LLM disponíveis na VPS — OpenCode Go completo, demais como stubs
timestamp: 2026-08-17T00:00:00-03:00
status: draft
---

# LLM Providers

Fonte da verdade sobre as APIs de modelo acessíveis por qualquer agente da VPS (Hermes, Claude Code, scripts). Como cada provider entra no Hermes e como trocar o modelo ativo.

## Visão geral

| Provider | Estado na wiki | No Hermes | Key env |
|---|---|---|---|
| OpenCode Go | ✅ completo | nativo (`opencode-go`) | `OPENCODE_GO_API_KEY` |
| DeepSeek Platform | ⚠️ stub | `deepseek` | `DEEPSEEK_API_KEY` |
| OpenRouter | ⚠️ stub | — | `OPENROUTER_API_KEY` |
| Google Gemini | ⚠️ stub | — | `GOOGLE_API_KEY` |
| Nvidia NIM | ⚠️ stub | `nvidia` | `NVIDIA_API_KEY` |
| Anthropic | ⚠️ stub | — | `ANTHROPIC_API_KEY` |

## OpenCode Go — completo

### O que é

Plano OpenCode Go (opencode.ai), assinatura mensal do Giovani com acesso a modelos open-source via API OpenAI-compatible (OpenCode Zen).

### Configuração

- Env var: `OPENCODE_GO_API_KEY` em `/root/.hermes/.env`
- Provider **nativo** do Hermes — nenhum bloco custom no config.yaml
- `model.provider: opencode-go` e `model.default: deepseek-v4-flash` no config.yaml
- Configurado via dashboard do Hermes (Settings → Providers), não por `hermes config set`

### Como usar

- Hermes: trocar modelo ativo com `hermes config set model.default <modelo>` (ou dashboard)
- Outros agentes/scripts: qualquer cliente OpenAI-compatible apontando para a base_url do Zen (`https://opencode.ai/zen/v1` — a confirmar)

### Limites

- Assinatura Go: ~$10/mês
- Modelos do plano: lista a confirmar

### Erros conhecidos

- Nenhum registrado até 2026-08-17

### Status de validação

- `draft` — base_url e lista de modelos do plano pendentes de confirmação

## Stubs — faltam informações

### DeepSeek Platform

- Key: `DEEPSEEK_API_KEY` · provider Hermes: `deepseek`
- Pendente: modelos disponíveis, uso atual

### OpenRouter

- Key: `OPENROUTER_API_KEY` · sem uso documentado
- Pendente: se ainda é usado, modelos

### Google Gemini

- Key: `GOOGLE_API_KEY` · sem uso documentado
- Pendente: uso, modelos

### Nvidia NIM

- Key: `NVIDIA_API_KEY` · provider Hermes: `nvidia`
- Sabe-se: `https://integrate.api.nvidia.com/v1` é usada no vision auxiliar (modelo minimax-m3)
- Pendente: modelos, uso atual

### Anthropic

- Key: `ANTHROPIC_API_KEY` · sem uso documentado na VPS
- Pendente: uso (não confundir com plano web)

## Conexões

- [[wiki/systems/hermes.md|Hermes]] — config de modelo
- [[wiki/systems/hermes-estado.md|Estado do Hermes]]