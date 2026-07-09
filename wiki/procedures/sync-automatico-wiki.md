---
type: procedure
tags: [wiki, git, cron, sincronizacao]
title: Sync automático da wiki (pull)
description: Cron que puxa a wiki do GitHub a cada 5 minutos, mantendo a cópia local da VPS atualizada com edições feitas em outros devices (Obsidian PC/celular)
timestamp: 2026-07-08T00:00:00-03:00
status: stable
---

# Sync automático da wiki (pull)

## O que faz

Mantém `/root/wiki` sincronizado com o remoto `omgiova/wiki` no GitHub, puxando mudanças feitas em outros devices (Obsidian no Windows ou Android) sem exigir `git pull` manual na VPS.

Complementa o outro lado da sincronização — o hook `post-commit` que já empurra (`push`) automaticamente qualquer commit feito na VPS (ver [[wiki/tools/obsidian-git.md|Obsidian Git]] e [[wiki/concepts/wiki.md|Wiki]]). Este procedure cobre o sentido contrário: GitHub → VPS.

## Gatilho

Cron do usuário `root`, a cada 5 minutos:

```
*/5 * * * * git -C /root/wiki pull --ff-only origin main >> /var/log/wiki-sync.log 2>&1
```

## Como executar

Automático — não requer ação manual. Para rodar manualmente: `git -C /root/wiki pull --ff-only origin main`.

## Entradas e saídas

- **Entrada:** estado do branch `main` em `omgiova/wiki` no GitHub
- **Saída:** working tree de `/root/wiki` atualizado; log em `/var/log/wiki-sync.log`

`--ff-only` faz o pull falhar (sem sujar o log além da mensagem de erro) se houver divergência que exigiria merge — protege contra sobrescrever trabalho local não commitado.

## Arquivos

- Crontab de `root` (`crontab -l`)
- `/var/log/wiki-sync.log`

## Resultado esperado

A cada 5 minutos, `/root/wiki` reflete o que está no GitHub. Se um agente ou o Giovani editar a wiki por outro device, a VPS converge em até 5 minutos sem intervenção.

## Conexões

- [[wiki/concepts/wiki.md|Wiki]] — visão geral da automação da wiki
- [[wiki/tools/obsidian-git.md|Obsidian Git]] — lado do push (hook `post-commit`)
- [[wiki/systems/vps.md|VPS]] — onde o cron roda
