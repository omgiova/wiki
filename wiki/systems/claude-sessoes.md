---
type: system
tags: [claude, sessoes, remote-control, tmux, vps]
title: Sessões do Claude na VPS (claude-sessao)
description: Sessões do Claude interativo rodando no tmux da VPS com Remote Control — comando claude-sessao, vigia, retomada, regras para agentes e erros conhecidos
timestamp: 2026-10-17T15:12:00-03:00
status: draft
---

# Sessões do Claude na VPS (claude-sessao)

## O que é

Cada sessão do Claude que o Giovani usa pelo app (claude.ai/code ou celular) é um processo `claude` **interativo** rodando numa sessão do tmux da VPS, com Remote Control ligado. Fica de pé sem nenhum terminal conectado.

Substituiu em 2026-10-17 o serviço `claude-remote` (modo servidor, `claude remote-control`). Motivo: no modo servidor a ferramenta de envio de arquivo falha (`not delivered: this session is not on a project thread`); no interativo funciona. Testado nos dois modos em 2026-10-17.

## Stack e configuração

| Peça | Caminho | Faz |
|---|---|---|
| Comando | `/usr/local/bin/claude-sessao` | abre, retoma, lista, procura, fecha; tem a lista de subcomandos no topo |
| Servidor tmux | `claude-tmux.service` (socket `tmux -L claude`, sessão `_base`) | dono dos processos; as sessões não morrem com quem as abriu |
| Vigia | `claude-sessao-vigia.timer` → `.service`, a cada 60 s | mantém uma sessão vazia, fecha as arquivadas, responde avisos de abertura |
| Registro | `/var/lib/claude-sessao/registro.log` | uma linha por abertura, retomada e fechamento (só acrescenta) |
| Histórico | `~/.claude/projects/-root/<id>.jsonl` | conversa de cada sessão (as do modo servidor antigo ficam em `-root-vps/`, e também se retomam) |

Toda sessão: pasta `/root`, `--permission-mode auto`, `--remote-control` sem nome (o app dá o título; o Giovani renomeia lá), id fixado com `--session-id`. No tmux a sessão se chama `s-<8 primeiros caracteres do id>` e guarda o id na opção `@claude_id`.

Limite: **8 sessões abertas** (cada uma usa ~350–430 MB de memória).

## Interface

```bash
claude-sessao listar                         # nome, id, estado (vazia | em uso | arquivada), memória, aberta em
claude-sessao abrir                          # sessão nova agora (normalmente o vigia faz)
claude-sessao procurar "cop-ia api"          # conversas antigas com esse texto nas mensagens do usuário (só lista)
claude-sessao procurar "raquetes" --dias 7
claude-sessao retomar 970cc2cc-9cf1-5c94-a078-48860a2de1ff   # volta no app com o histórico
claude-sessao ver 970cc2cc                   # tela atual da sessão
claude-sessao fechar 970cc2cc                # fecha o processo; o histórico fica
claude-sessao renomear 970cc2cc "Novo nome"  # dá nome à sessão (digita só "/rename <nome>")
claude-sessao vigiar                         # uma rodada do vigia, à mão

systemctl status claude-sessao-vigia.timer
journalctl -u claude-sessao-vigia -n 50
```

O id aceita o começo, se não for ambíguo.

Pedidos do Giovani e o que fazer:
- "lista as sessões" → `listar`
- "retoma a sessão sobre X" → `procurar` → mostrar candidatas (id, data, primeira mensagem) → ele escolhe → `retomar`
- "fecha a sessão X" → `fechar`
- "renomeia a sessão X para Y" → `renomear` (vale para qualquer sessão aberta, inclusive a própria)
- "a sessão X sumiu do app" → `ver X` e dizer o que está na tela

## Operação

**Vigia, a cada rodada:**
1. Se o histórico da sessão tem a mensagem de sistema "Remote Control disconnected — this session was ended or archived from another device or app (code 4090)" com horário depois da abertura atual da sessão no tmux, considera arquivada no app e fecha o processo. Só conta depois da abertura porque uma sessão retomada traz no histórico os arquivamentos antigos. Só vale a entrada `type: system`; o texto "code 4090" escrito numa conversa não conta.
2. Se a tela de uma sessão tem a pergunta de confiança na pasta ou o aviso do modo auto, responde (Down+Enter / "Don't show again").
3. Se não há nenhuma sessão vazia (histórico sem mensagem do usuário) e há menos de 8 abertas, abre uma.

**Arquivar:** só pelo app, pelo Giovani. Ao arquivar, o processo na VPS continua rodando. O Claude grava a mensagem "code 4090" no histórico na hora do arquivamento, e o vigia fecha o processo na rodada seguinte.

**Nome no `listar`:** o último título automático que o Claude deu à conversa (`ai-title` no histórico); sem título, o começo da primeira mensagem. Renomear no app não muda esse nome: o nome dado no app não fica gravado na VPS.

> Observação (2026-10-17 15:01, teste com a sessão 970cc2cc): o nome dado no app **fica gravado** na VPS, como entrada `custom-title` no histórico (e em `<id>/custom-title.json`). O `listar` passou a mostrar primeiro esse nome; sem ele, o `ai-title`; sem título, o começo da primeira mensagem.

**Renomear:** `claude-sessao renomear <id> "<nome>"` digita `/rename <nome>` na tela da sessão e Enter; não digita nenhum outro texto. Antes confere a tela: se há pergunta aberta (ex.: pedido de aprovação) ou texto na caixa, recusa. Nome limpo de quebras de linha e caracteres de controle, até 80 caracteres. Registra "renomeada" no registro. Se a sessão está respondendo, o comando entra na fila e roda quando ela termina. No modo auto, `tmux send-keys` é bloqueado ("Tmux Self Drive"); por isso há só a regra `Bash(claude-sessao renomear *)` em `/root/.claude/settings.local.json`, e o `send-keys` continua bloqueado (evita `/clear` ou outro texto). Testado em 2026-10-17 15:11: esta sessão (08c652a2) virou "Teste456" no app.

**Retomar:** `claude --resume <id>` num processo novo. Testado em 2026-10-17: sessão interativa antiga, sessão do modo servidor e sessão arquivada voltaram com o histórico e reconectadas ao app (a arquivada voltou no mesmo endereço).

**Boot:** `claude-tmux` sobe com a VPS; o timer roda 30 s depois e abre a sessão vazia.

### Regras para agentes (nunca)

1. Nunca digitar numa sessão alheia com `tmux send-keys` para dar instrução (o classificador do modo auto bloqueia, e é proibido). Falar com outra sessão: `SendMessage`. A única exceção é o `/rename`, e só pelo `claude-sessao renomear`.
2. Nunca `kill` em processo `claude`. Fechar só com `claude-sessao fechar`.
3. Nunca retomar conversa que já está aberta; o comando recusa e não se força. Dois processos na mesma conversa estragam o histórico.
4. Nunca religar `claude-remote.service` nem rodar `claude remote-control` (modo servidor).
5. Nunca abrir sessão em outra pasta que não `/root`.
6. Nunca arquivar sessão nem apagar histórico (`.jsonl`).
7. Nunca retomar sem o Giovani confirmar qual, a partir do `procurar`.
8. Nunca passar de 8 sessões abertas; se pedirem mais, avisar e sugerir quais fechar.

## Erros conhecidos

| Sintoma | Primeiro passo | Causa comum |
|---|---|---|
| sessão não aparece no app | `claude-sessao ver <id>` | pergunta parada na tela; login vencido; cota esgotada |
| "offline" no app | `claude-sessao listar` | arquivada (o vigia fecha) ou processo caiu → `retomar` |
| arquivo não chega ao usuário | conferir se a sessão é do `claude-sessao` | sessão de modo servidor não envia arquivo |
| pergunta "trust this folder" toda vez em `/root` | nenhum (o comando responde) | o Claude não grava a confiança da pasta pessoal (`hasTrustDialogAccepted` fica `false` para `/root`) |
| sessão ativa fechada sozinha, registro diz "fechada (arquivada no app)" | `tail /var/lib/claude-sessao/registro.log` | versão antiga do vigia: fechava quando a frase `/remote-control is active` sumia da tela, mas ela só aparece no começo da conversa e sai da tela quando a conversa cresce. Corrigido em 2026-10-17: o vigia passou a usar a mensagem "code 4090" do histórico (ver Operação); retomar com `claude-sessao retomar <id>` |
| sessão retomada abre em modo manual | usar sempre o comando | `--resume` sem `--permission-mode auto` volta no modo manual |

## Conexões

- [[wiki/systems/vps.md|VPS]] — máquina onde as sessões rodam
- [[wiki/systems/termux-ssh-claude.md|Termux + SSH + Claude]] — acesso anterior por terminal e diagnóstico de 2026-06-26
- [[wiki/systems/hermes.md|Hermes]] — também consegue rodar `claude-sessao` pelo Telegram
