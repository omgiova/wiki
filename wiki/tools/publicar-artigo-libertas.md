---
type: tool
tags: [tools, libertas, seo, publicacao, automacao, git, cron, agendamento]
title: Script publicar-artigo.sh (Libertas)
description: Script genérico que facilita o agendamento e a publicação de qualquer artigo do blog da Libertas via cron nativo — abre branch nova a partir da main, carimba a data real de publicação no frontmatter, commita os arquivos indicados, dá push e abre o PR. v1.1.0.
timestamp: 2026-08-28T18:09:16-03:00
status: draft
---

# Script `publicar-artigo.sh` — publicação/agendamento de artigos da Libertas

## Versão

**v1.1.0** — 28/08/2026. Passa a **carimbar a data real da publicação** no frontmatter (`date`/`lastmod`) de cada `.md` no momento em que roda.

**v1.0.0** — criado em **26/08/2026**. Primeira execução real agendada para **28/08/2026 00:00 (BRT)** (publicação do artigo de planejamento financeiro).

> ✅ **Validado em produção em 28/08/2026:** o cron disparou 00:00 BRT, abriu o PR #24 e a Luciana mergeou. O mecanismo funciona.
>
> ⚠️ **Bug encontrado nessa primeira execução (corrigido na v1.1.0):** o artigo saiu publicado com a data errada (`2026-08-19`, dia em que foi escrito) porque o v1.0.0 publicava o `.md` **como estava** — e o Hugo exibe a data do campo `date:` do frontmatter, **não** a data do commit. A data interna nunca era atualizada. Corrigido no PR #25 (data do artigo) e no script (v1.1.0 carimba automaticamente).

## Função

Facilitar o **agendamento e a publicação** de qualquer artigo do blog da Libertas, independente de formato ou de quantos recursos (imagens, etc.) ele tenha. É uma peça reutilizável: para publicar um artigo em um horário marcado, agenda-se uma linha no **cron nativo do Linux** chamando este script com a branch, o título e a lista de arquivos daquele artigo.

Respeita a regra do projeto: **toda alteração abre uma branch NOVA a partir da `main`** (nunca reusa branch). O **merge continua manual, feito pela conta da Luciana** — o script só vai até abrir o PR.

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

# Carimba a data REAL da publicação no frontmatter de cada .md do artigo.
# Motivo: o Hugo exibe a data do campo `date:` do arquivo, não a do commit.
# Sem isso, um artigo escrito dias antes sai publicado com a data antiga.
# Substitui só o YYYY-MM-DD de `date:`/`lastmod:`, preservando o horário.
hoje=$(TZ=America/Sao_Paulo date +%Y-%m-%d)
for f in "$@"; do
  case "$f" in
    *.md)
      sed -i -E "s/^(date: )[0-9]{4}-[0-9]{2}-[0-9]{2}/\1${hoje}/;s/^(lastmod: )[0-9]{4}-[0-9]{2}-[0-9]{2}/\1${hoje}/" "$f"
      ;;
  esac
done

git add -- "$@"
git commit -qm "Publica: $titulo"
git push -qu origin "$branch"
gh pr create --base main --head "$branch" --title "$titulo" \
  --body "Branch a partir da main. Arquivos: $*"
```

## Como funciona

1. `set -euo pipefail` — aborta em qualquer erro (não publica pela metade).
2. `cd /root/libertas` — entra no repositório do site.
3. Lê os argumentos: `branch`, `titulo`, e o `shift 2` deixa em `"$@"` **a lista de arquivos** do artigo.
4. `git fetch -q origin main` — atualiza a referência da `main`.
5. `git checkout -qB "$branch" origin/main` — cria a branch **nova a partir da `main`**. Arquivos ainda não rastreados (o `.md` e as imagens do artigo) permanecem intactos no diretório.
6. **Carimba a data (v1.1.0):** para cada `.md` da lista, reescreve o `YYYY-MM-DD` dos campos `date:` e `lastmod:` com a **data de hoje em BRT**, preservando o horário que já estava no arquivo. Só mexe em `.md` (imagens `.webp` são ignoradas). Garante que o post apareça no site com a data real da publicação, não com a data em que foi escrito.
7. `git add -- "$@"` — adiciona **apenas** os arquivos passados. Nada mais entra no commit.
8. `git commit` / `git push` — commita e envia a branch pro GitHub (via chave SSH da VPS).
9. `gh pr create` — abre o PR contra a `main`. O merge fica com a Luciana.

Como o script lê os arquivos **no momento em que roda**, qualquer ajuste feito no artigo antes do horário agendado já entra automaticamente. A data também é definida nesse momento, então **não importa quando o artigo foi escrito** — ele sempre sai com a data do dia em que o cron dispara.

## Agendamento (cron nativo do Linux)

Usa-se o `crontab -l` do root — o mesmo cron nativo que já roda outros jobs da VPS, sem instalar nada externo. Como o cron não tem campo de ano, um agendamento **de uma vez só** é feito com a própria linha **se auto-removendo** após disparar, via um marcador único.

Exemplo — publicação do planejamento financeiro em 28/08/2026 00:00 (linha real hoje no crontab):

```cron
0 0 28 8 * /root/libertas/scripts/publicar-artigo.sh pub/planejamento-financeiro "Planejamento financeiro empresarial" content/blog/planejamento-financeiro-empresarial.md static/images/planejamento-financeiro-empresarial.webp static/images/planejamento-financeiro-padaria.webp >> /root/libertas/.publish.log 2>&1; crontab -l | grep -v PLANEJAMENTO_ONESHOT | crontab - # PLANEJAMENTO_ONESHOT
```

- `0 0 28 8 *` — 00:00 do dia 28/08 (fuso da VPS é `America/Sao_Paulo -03`, então já é BRT).
- Saída vai para `/root/libertas/.publish.log`.
- `crontab -l | grep -v PLANEJAMENTO_ONESHOT | crontab -` — depois de rodar, remove a própria linha (identificada pelo marcador `# PLANEJAMENTO_ONESHOT`), sem deixar lixo nem repetir no ano seguinte.

Para agendar outro artigo: nova linha de cron com data, nome de branch, título, lista de arquivos e um marcador `_ONESHOT` próprio. Nada mais.

## Pré-requisitos / gotchas

- **Cron nativo** já ativo na VPS (`cron`/`crontab`), sem instalar nada.
- **Fuso:** VPS em `America/Sao_Paulo (-03)` — o horário do cron já é BRT.
- **`gh` autenticado** como `omgiova` e **push por chave SSH** da VPS (já configurados no repo).
- Cada agendamento único precisa de um **marcador `_ONESHOT` distinto** para a auto-remoção não apagar outras linhas.

## Conexões

- Documento central do projeto: [[Libertas-SEO]]
- Processo de produção do artigo: [[criar-artigo-seo-libertas-skill]]
