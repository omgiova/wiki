---
type: session
tags: [vps, docker, incidentes, memoria, swap]
title: "Sessão: 2026-09-21 — VPS travou por sobrecarga + criação de swap"
description: VPS congelou ~20h08 (load ~44), Docker Swarm reiniciou todos os serviços; finflow dev e promptgolf desligados; swap de 2GB criado
timestamp: 2026-09-21T20:40:00-03:00
status: draft
---

# Sessão: 2026-09-21 — VPS travou por sobrecarga + criação de swap

## O que aconteceu

- Painel da Hostinger mostrava memória em ~80% (7,55 GB).
- Por volta das **20h08 (BRT)** a VPS congelou: load average chegou a **~44** (VPS de 2 núcleos). Logs do dockerd chegavam com ~2 min de atraso e registravam `heartbeat to manager failed` / `context deadline exceeded` e perda de liderança do raft do Swarm.
- Por volta das **20h20** o Docker Swarm reiniciou **todos** os serviços: n8n (editor, worker, 2× webhook), Evolution API, Postgres, Redis, Node-RED, promptgolf, EasyPanel e Traefik. Serviços ficaram fora do ar durante esse intervalo.
- `journalctl -k` **não** registrou OOM killer — nenhum processo foi morto por falta de memória.

## Causa

**Não confirmada.** Hipótese: memória quase cheia sem swap levando a VPS a travar. O Giovani suspeita de um agente que alterava fluxos no n8n pouco antes; não foi possível confirmar pelos logs.

## Estado encontrado (antes das ações)

- Servidor `next dev` do finflow (`/root/finflow`) esquecido rodando há ~23h na porta 3001 (exposta em `*`), ~1,3 GB de RAM.
- VS Code remoto: ~1,5 GB enquanto conectado.
- Um `git index-pack` (~230 MB) apareceu junto com o reinício dos serviços e terminou sozinho; origem não identificada.

## Ações tomadas (autorizadas pelo Giovani)

1. **finflow dev desligado** (`kill` nos processos `next dev` / `next-server`); porta 3001 fechada.
2. **promptgolf desligado** com `docker service scale promptgolf=0` — config mantida no EasyPanel. ⚠️ Esse comando contraria a regra de prevenção em [[wiki/systems/vps.md|Infraestrutura do VPS]] (risco de IPVS vazio); a conectividade dos demais serviços não foi verificada depois.
3. **Swap de 2 GB criado:** `/swapfile` (chmod 600), entrada em `/etc/fstab` (ativo no boot), `vm.swappiness=10` em `/etc/sysctl.d/99-swap.conf`.

Disco após o swap: 96 GB, ~65 GB usados, ~30 GB livres.

## Conexões

- [[wiki/systems/vps.md|Infraestrutura do VPS]]
- [[wiki/systems/n8n.md|n8n]]
