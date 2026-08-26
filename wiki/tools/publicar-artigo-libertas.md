---
type: tool
tags: [tools, libertas, seo, publicacao, automacao, git, agendamento]
title: Script publicar-artigo.sh (Libertas)
description: Script genérico que facilita o agendamento e a publicação de qualquer artigo do blog da Libertas — abre branch nova a partir da main, commita os arquivos indicados, dá push e abre o PR. v1.0.0.
timestamp: 2026-08-26T20:55:00-03:00
status: draft
---

# Script `publicar-artigo.sh` — publicação/agendamento de artigos da Libertas

## Versão

**v1.0.0** — criado em **26/08/2026**. Primeira execução real agendada para **28/08/2026 00:00 (BRT)** (publicação do artigo de planejamento financeiro).

> ⚠️ **Não testado e não validado.** Nesta data (26/08/2026) o script foi apenas escrito e documentado; ainda não rodou nenhuma vez. A primeira execução será a de 28/08/2026 — só depois dela dá para considerar validado.

## Função

Facilitar o **agendamento e a publicação** de qualquer artigo do blog da Libertas, independente de formato ou de quantos recursos (imagens, etc.) ele tenha. Em vez de um script descartável por artigo, é uma peça reutilizável: para publicar um novo artigo agendado, basta chamar o script passando a branch, o título e a lista de arquivos daquele artigo.

Respeita a regra do projeto: **toda alteração abre uma branch NOVA a partir da `main`** (nunca reusa branch). O **merge continua sendo manual, feito pela conta da Luciana** — o script só chega até abrir o PR.

## Localização

`/root/libertas/scripts/publicar-artigo.sh`

## Código (verbatim)

```bash
#!/usr/bin/env bash
# uso: publicar-artigo.sh <branch> <titulo> <arquivo...>
set -euo pipefail
cd /root/libertas
branch=$1; titulo=$2; shift 2
git fetch -q origin main
git checkout -qB "$branch" origin/main
git add -- "$@"
git commit -qm "Publica: $titulo"
git push -qu origin "$branch"
gh pr create --base main --head "$branch" --title "$titulo" \
  --body "Branch a partir da main. Arquivos: $*"
```

## Como funciona

1. `set -euo pipefail` — aborta em qualquer erro (não publica pela metade).
2. `cd /root/libertas` — entra no repositório do site.
3. Lê os argumentos: `branch` (nome da branch), `titulo` (do commit e do PR), e o `shift 2` deixa em `"$@"` **a lista de arquivos** do artigo.
4. `git fetch -q origin main` — atualiza a referência da `main`.
5. `git checkout -qB "$branch" origin/main` — cria a branch **nova a partir da `main`**. Arquivos ainda não rastreados (o `.md` e as imagens do artigo) permanecem intactos no diretório.
6. `git add -- "$@"` — adiciona **apenas** os arquivos passados. Nada mais entra no commit.
7. `git commit` / `git push` — commita e envia a branch pro GitHub.
8. `gh pr create` — abre o PR contra a `main`. O merge fica com a Luciana.

Como o script lê os arquivos **no momento em que roda**, qualquer ajuste feito no artigo antes do horário agendado já entra automaticamente.

## Agendamento (one-shot com `at`)

Ferramenta escolhida: **`at`** (dispara uma vez e se auto-remove — mais limpo que cron para tarefa única). Requer o pacote `at` instalado e o daemon `atd` ativo.

Exemplo — agendar a publicação do planejamento financeiro para 28/08/2026 00:00:

```bash
echo '/root/libertas/scripts/publicar-artigo.sh pub/planejamento-financeiro \
"Planejamento financeiro empresarial" \
content/blog/planejamento-financeiro-empresarial.md \
static/images/planejamento-financeiro-empresarial.webp \
static/images/planejamento-financeiro-padaria.webp' | at 00:00 2026-08-28
```

Para publicar outro artigo no futuro: trocar o nome da branch, o título e a lista de arquivos. Nada mais.

## Pré-requisitos / gotchas

- **Fuso:** VPS em `America/Sao_Paulo (-03)` — o horário do `at` já é BRT.
- **`at` não vinha instalado** na VPS (26/08/2026); precisa `apt-get install -y at` + `systemctl enable --now atd`.
- **`gh` autenticado** como `omgiova` (já configurado no repo).

## Conexões

- Documento central do projeto: [[Libertas-SEO]]
- Processo de produção do artigo: [[criar-artigo-seo-libertas-skill]]
