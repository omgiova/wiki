---
type: concept
tags: [projects, gtd, fluxo-de-trabalho, telegram, tralhasbot, sqlite]
title: Fluxo de trabalho (GTD)
description: Sistema do Giovani para administrar o fluxo de trabalho pelo método do livro A Arte de Fazer Acontecer (GTD) — bot do Telegram @tralhasbot como caixa de entrada (só guarda, sem LLM), banco SQLite na VPS e processamento por agente Claude em sessão; fase 1 em uso desde 14/10/2026.
timestamp: 2026-10-14T12:15:00-03:00
status: draft
---

# Fluxo de trabalho (GTD)

Lugar único para qualquer coisa que o Giovani precise lembrar, fazer, decidir ou guardar,
organizado pelo método do livro *A Arte de Fazer Acontecer* (David Allen, GTD): coletar,
processar, organizar, revisar e executar.
Criado em 14/10/2026 (BRT).

## Por que existe

Ideias e pendências nascem no meio do trabalho (numa sessão com o Claude, no celular,
numa conversa) e se perdem, ou ficam espalhadas em memórias de agente, listas e arquivos.
O Giovani não consulta a wiki nem listas no dia a dia, então o sistema precisa estar onde
ele já está: o Telegram.

## Como o método se divide

| Estágio do livro | Quem faz | Onde |
|---|---|---|
| Coletar | Giovani | Bot do Telegram `@tralhasbot` |
| Processar e organizar | Agente Claude, em sessão | Banco SQLite na VPS |
| Revisar | Agente prepara, Giovani decide | Sessão / Telegram |
| Executar | Giovani | — |

## Decisões do Giovani

- O bot é novo e próprio, separado do Hermes.
- O bot não tem LLM e não responde nada: só guarda o que chega.
- Quem processa é um agente Claude, acionado em sessão; o resultado pode ser enviado ao
  Telegram pelo próprio agente.
- Canal: Telegram (preferido ao WhatsApp pelas funções interativas).
- Não usar o OM Hub nem o Supabase da Open Mídia para este sistema.
- Fase 1: nada é automático. O Giovani decide quando acontece cada etapa, para ver o fluxo
  funcionando antes de automatizar.
- O sistema vai ter uma interface (UI), em etapa posterior.

## Fase 1 — o que está no ar

```
Giovani (Telegram) ──mensagem──▶ @tralhasbot (serviço tralhasbot na VPS)
                                        │  guarda, não responde
                                        ▼
                     SQLite: /root/projects/metodo-pendencias/dados/pendencias.db
                                        │  quando o Giovani pede, numa sessão
                                        ▼
                           agente Claude processa / mostra / revisa
```

### Bot

- Usuário: `@tralhasbot`.
- Aceita só o Giovani, e só em conversa privada; mensagem de qualquer outra pessoa ou de
  grupo é ignorada.
- Guarda texto, voz, áudio, foto, vídeo, documento, figurinha, GIF, localização, contato e
  mensagem encaminhada, com data e hora de Brasília.
- Arquivos não são baixados para a VPS: o bot guarda só o `file_id` do Telegram.
- Mensagem editada entra como linha nova; a original continua guardada.
- Lê o Telegram por long polling (sem webhook, sem porta aberta). Mensagens enviadas com o
  bot desligado ficam no Telegram por 24 horas e são recolhidas quando ele volta.

### Serviço

| Item | Valor |
|---|---|
| Unidade systemd | `/etc/systemd/system/tralhasbot.service` |
| Comportamento | sempre ligado; reinicia em 10 s se cair; sobe com a VPS |
| Memória | ~11 MB |

```bash
systemctl status tralhasbot     # ver se está rodando
systemctl stop tralhasbot       # desligar
systemctl start tralhasbot      # ligar
journalctl -u tralhasbot -n 50  # últimas linhas do log
```

### Arquivos

| Caminho | O que é |
|---|---|
| `/root/projects/metodo-pendencias/bot.py` | Bot (Python, só biblioteca padrão) |
| `/root/projects/metodo-pendencias/banco.py` | Esquema e acesso ao SQLite |
| `/root/projects/metodo-pendencias/dados/pendencias.db` | Banco |
| `/root/projects/metodo-pendencias/fichamento.md` | Fichamento do livro (caps. 2, 3, 5–10) e síntese proposta |
| `/root/.config/pendencias/bot.env` | Token do bot e ID do dono (chmod 600; nunca copiar para repositório, wiki ou conversa) |

### Banco

Tabela `mensagens`: caixa de entrada bruta. Cada linha é uma mensagem como chegou; nunca é
editada nem apagada. O processamento só preenche `processada_em`.

| Coluna | Conteúdo |
|---|---|
| `recebida_em` | data e hora (ISO 8601, BRT) |
| `origem` | `telegram` ou nome do agente |
| `tipo` | texto, voz, audio, foto, video, documento, encaminhada, ... |
| `texto` | texto ou legenda |
| `arquivo_id` / `arquivo_nome` | `file_id` e nome do arquivo no Telegram |
| `editada` | 1 se for versão editada de outra mensagem |
| `telegram_msg` | `message_id` no Telegram |
| `bruto` | JSON original da mensagem |
| `processada_em` | quando foi processada (vazio = ainda na entrada) |

Tabela `estado`: guarda o `telegram_offset` (até onde o bot já leu).

## Como usar na fase 1

O Giovani manda qualquer coisa ao `@tralhasbot`. Para as outras etapas, pede numa sessão:

| Pedido | O que acontece |
|---|---|
| "o que tem na entrada?" | O agente mostra o que chegou, sem processar |
| "processa a entrada" | O agente processa as mensagens pendentes pelo método do livro e mostra o resultado e as perguntas para o Giovani |
| "faz a revisão" | O agente segue o roteiro da revisão semanal do livro |
| "me manda X no Telegram" | O agente envia pelo bot (única hora em que ele fala) |

## Em aberto

- Regras do processamento e compartimentos finais (proposta na síntese do fichamento, não validada).
- Porta para agentes gravarem itens na mesma entrada.
- Destino privado para referência de cliente (a wiki é pública).
- Lembretes por data, itens suspensos, repetição e aviso de revisão.
- Comandos ou botões no Telegram.
- Interface: wireframe com 5 opções de tela inicial (1A abas por compartimento, 1B painel
  com blocos, 1C por cliente ou área, 1D filtro de momento, 1E linha do tempo) aguardando
  escolha do Giovani.

## Conexões

- [[wiki/projects/bootcamp-copy-automacao.md|Bootcamp — painel e automações de copy]] — outro sistema com agente acionado sob demanda na VPS
