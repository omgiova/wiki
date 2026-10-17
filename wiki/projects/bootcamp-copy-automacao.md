---
type: concept
tags: [projects, copy, bootcamp, redatores, claude-code, supabase, fila, libertas]
title: Bootcamp — painel e automações de copy
description: Documentação técnica do sistema de copy por agentes — sala dos redatores (/opt/redatores), comandos de terminal, fila no Supabase com vigia na VPS e painel https://cop-ia.netlify.app/bootcamp.
timestamp: 2026-10-17T16:40:00-03:00
status: draft
---

# Bootcamp: painel e automações de copy

Documentação técnica para quem for mexer no sistema. Atualizada em 13/10/2026 (BRT).

O sistema é uma coisa só, com quatro partes:

1. **Sala dos redatores** (`/opt/redatores`): onde as sessões do Claude escrevem, revisam e refazem peças.
2. **Comandos de terminal** (`/usr/local/bin`): encadeiam as sessões.
3. **Fila** (tabela `fila` no Supabase + vigia na VPS): deixa o painel disparar os comandos sem terminal.
4. **Painel** (`https://cop-ia.netlify.app/bootcamp`): mostra as peças, as notas e as execuções, e dispara pedidos.

```
painel (bootcamp.html) ──insere linha──▶ tabela fila (Supabase)
                                              │  lida a cada minuto
                                              ▼
                              fila.py (VPS, systemd timer)
                                              │  executa um comando da lista permitida
                                              ▼
                 produzir / escrever-lote / revisar-lote / refazer-lote / triar
                                              │  abre sessões `claude -p "/r ..."` na sala
                                              ▼
                 sala: comandos /r /rv /refazer + ferramentas/copy.py
                                              │  deposita a peça
                                              ▼
                 biblioteca do editor (tabela projetos) ──▶ painel lê e mostra
```

## Regras que não podem ser quebradas

- Nunca apagar nada: peça, linha do banco, registro, teste. Versão nova em vez de substituir.
- Antes de alterar qualquer arquivo da sala ou comando de terminal, copiar a versão atual para `/opt/redatores/_versoes/<data-hora>-<nome>/`.
- Não gravar anotações dentro de `/opt/redatores`: tudo o que está lá pode entrar no contexto do redator. Anotações ficam em `/opt/redatores-notas/`.
- Abrir sessão gasta a cota do Giovani. Não abrir sessão sem ordem dele.
- Não subir Chromium, servidor nem teste pesado na VPS.
- O sorteio de combinações não deve ser alterado.
- Ideias que o Giovani já descartou: referência do redator é só post publicado da marca; o corte de nota vale para a IA; o briefing pode vir só com o título.
- Credenciais nunca vão para repositório, wiki ou conversa.

## 1. Sala dos redatores (`/opt/redatores`)

| Caminho | O que é |
|---|---|
| `CLAUDE.md` | Regras da sala, lidas por toda sessão |
| `.claude/commands/r.md` | Comando `/r <ticket>`: redator escreve uma peça nova |
| `.claude/commands/rv.md` | Comando `/rv <ticket> <peça>`: revisor dá nota |
| `.claude/commands/refazer.md` | Comando `/refazer <ticket> <peça>`: refaz a partir da revisão |
| `.claude/settings.json` | Permissões: libera `copy.py`, leitura da pasta e Skill; nega edição, subagentes, memória e wiki |
| `tickets/NNN.md` | Briefing de cada post |
| `marca/libertas/` | Material da marca |
| `blocos.md` | Blocos que o sorteio combina |
| `rounds.json` | O que mudou no sistema a cada rodada, por ticket (o painel mostra) |
| `ferramentas/copy.py` | Lê ticket, sorteia combinação, confere e deposita a peça, gera os dados do painel |
| `ferramentas/triagem.py` | Decide o destino da peça pela nota e copia a aprovada para a entrega |
| `ferramentas/feedback.py` | Lê o feedback por frase deixado no painel |
| `ferramentas/fila.py` | Vigia da fila (parte 3) |
| `registros/NNN/` | Toda tentativa de cada ticket, aceita ou recusada |
| `registros/sorteios.jsonl` | Cada combinação sorteada |
| `registros/lotes/` | Saída bruta de cada sessão aberta por comando |
| `_versoes/` | Cópia de tudo o que já foi substituído |

Cada script tem a lista de subcomandos no topo do arquivo.

**Nomes das peças:** `R17v1` é a primeira versão do redator 17. Refeita vira `R17v1.1`, e a refeita dela `R17v1.1.1`.

**Coluna `projetos.nome` (17/10/2026):** guarda só o título, verbatim como vem do ticket/Trello. O prefixo `NNN · Rxx · Sxxxx | ` e o sufixo `(vN)` deixaram de ser gravados; ticket, redator e sorteio ficam nas tags (`ticket-`, `redator-`, `sorteio-`) e a versão em `dados.bootcamp.versao`. Vale para as duas salas (`/opt/redatores` e `/opt/redatores-lat`). Scripts e painel identificam a peça pelas tags e pelo `codigo`, nunca pelo nome; o título do painel vem de `dados.bootcamp.briefing.titulo`. As 141 peças antigas foram limpas (nome, `dados.nome` e 1ª linha do `md`); o estado anterior está na tabela `projetos_nome_backup_20261017`. As 9 peças de teste do ticket 000 foram para a lixeira.

**Nota (soma 100):** briefing 15, tom de voz 15, legenda 15, sweep zero 12, clareza 12, especificidade 12, fixação 7, emoção 5, prova 2, voz 2, combinação 3. Os pesos estão em `PESOS_SCORE` (`copy.py`), na lista de `rv.md` e no array `SCORE` de `bootcamp.html`. Os três têm de mudar juntos. Revisões anteriores a 13/10/2026 usam pesos antigos.

**Corte:** 85 ou mais entrega; 60 a 84 refaz; abaixo de 60 para.

## 2. Comandos de terminal (`/usr/local/bin`)

| Comando | Faz |
|---|---|
| `produzir <ticket> <qtd> [modelo do redator]` | Fluxo completo por peça: escreve, revisa, e refaz até três camadas. Variáveis `REVISOR` e `REFAZER` (padrão `sonnet`) |
| `escrever-lote <ticket> <qtd>` | Só escreve. Variável `MODELO` |
| `revisar-lote <ticket> <peças...>` | Só revisa. Variável `MODELO` |
| `refazer-lote <ticket> <peças...>` | Só refaz. Variável `MODELO` |
| `triar <ticket> [--ver]` | Aplica o corte nas peças já revisadas. `--ver` só mostra o plano |
| `redator` | Abre o Claude interativo dentro da sala |

Toda sessão é aberta assim:

```
claude -p "/r 003" --tools "Bash,Read,Skill" [--model X] \
  --setting-sources project --strict-mcp-config \
  --settings '{"model":"opus","effortLevel":"low"}' \
  [--output-format stream-json --verbose]
```

- `--setting-sources project` e `--strict-mcp-config` impedem que a sessão carregue os plugins, skills e conectores da conta do Giovani. Sem elas, cada chamada começa com cerca de 30 mil tokens; com elas, cerca de 9,5 mil (medido em 13/10/2026).
- `--settings` repõe o modelo padrão e o esforço que vinham das configurações do usuário, que deixaram de ser lidas.
- `SAIDA_JSON=1` liga a saída detalhada, que a fila usa para medir tokens, custo e limite da conta.
- O binário fica em `/root/.hermes/node/bin/claude`.

## 3. Fila

**Tabela `public.fila`** (definição em `/root/cop-ia/supabase/fila.sql`): `id, comando, parametros, estado, resposta, detalhes, criado_em, iniciado_em, terminado_em`. Estados: `na fila`, `rodando`, `terminou`, `falhou`.

Quem pode o quê:

- Qualquer visitante lê a fila e pode inserir só o comando `teste`.
- Só quem está logado no site insere os comandos que gastam cota.
- Ninguém altera linha pelo site. Só a VPS, com a chave de serviço.

**Vigia:** `fila-redatores.timer` roda `fila-redatores.service` uma vez por minuto, que executa `/opt/redatores/ferramentas/fila.py`. Ele pega o pedido mais antigo, um por vez (trava em `/run/fila-redatores.lock`), executa e grava o andamento na própria linha.

Segurança do vigia: só executa o que está em `COMANDOS`. A função `monta()` confere cada parâmetro (ticket tem de existir, quantidade de 1 a 5, modelo da lista `MODELOS`, peça no formato `R1v1.1`) e monta o comando em lista, sem passar por shell. Texto vindo da fila nunca vira comando.

O campo `detalhes` (JSON) guarda o que o painel mostra: modelo, durações, turnos, tokens, custo, ferramentas, briefing recebido, combinações sorteadas, etapas (uma por sessão), peças criadas e `uso` (fração do limite de 5 horas e da semana no primeiro e no último aviso da execução).

**Para acrescentar um comando à fila:** incluir em `COMANDOS` e em `monta()` (`fila.py`), na política `inclui_logado` (`fila.sql`, aplicar no banco) e em `CMD_ACOES` (`bootcamp.html`).

Operação:

```
systemctl status fila-redatores.timer        # o vigia está ligado?
journalctl -u fila-redatores.service -n 50   # o que ele fez
FILA_SECO=1 python3 /opt/redatores/ferramentas/fila.py   # roda o encanamento sem abrir sessão
```

## 4. Painel

Arquivo único: `/root/cop-ia/bootcamp.html` (HTML, CSS e JavaScript juntos, sem etapa de build). Repositório no GitHub, ramo `main`; o Netlify publica a cada push.

- Lê as peças da tabela `projetos` do Supabase e os dados extras de `/bootcamp-dados.json`, gerado na VPS por `python3 ferramentas/copy.py painel` (briefings, sorteios, revisões, rodadas).
- Cabeçalho: seletor de execução (`cmdMonta`, estado em `CMD`). O botão Executar chama `filaPedeLogado`; o Testar chama `filaPede`.
- Aba Fila: um card por pedido, montado a partir de `FILA_LISTA` (`filaVe` busca os 50 últimos e repete a cada 5 s enquanto houver pedido pendente).
- Login: o da página inicial do site (supabase-js). O painel lê a sessão do `localStorage` em `tokenLogin()`.

Como conferir uma alteração sem navegador:

```
python3 -c "import re;open('/tmp/b.js','w').write('\n'.join(re.findall(r'<script>(.*?)</script>',open('bootcamp.html').read(),re.S)))" && node --check /tmp/b.js
```

Isso só pega erro de sintaxe. O resultado visual quem confere é o Giovani.

## Onde ficam as credenciais

- Banco (chave de serviço e acesso direto): `/root/.config/copy/supabase.env`
- Login do site: `/root/.config/copy/login.env`

## Pontos em aberto

- O botão fixo "Produzir 003" da aba Fila ficou redundante depois do seletor.
- O Executar não pede confirmação.
- O custo mostrado é estimativa a preço de API; a conta é por assinatura.
- Hipótese ainda não testada sobre a legenda: `/opt/redatores-notas/hipotese-legenda.md`.

## Conexões

- Cópia de origem deste arquivo: `/opt/redatores-notas/DOCUMENTACAO.md`
