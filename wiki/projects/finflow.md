---
type: concept
tags: [projects, finflow, nextjs, google-sheets, dashboard]
title: finflow
description: Dashboard de gestão financeira pessoal em /root/finflow — Next.js + Planilha Google como banco; documentação completa vive no próprio repo
timestamp: 2026-07-06T16:37:00-03:00
status: stable
---

# finflow

Dashboard de **gestão financeira pessoal** do Giovani. Tema escuro com verde-água, sem banco de
dados nem backend próprio: uma **Planilha Google** é a fonte da verdade, acessada por um Apps
Script publicado como App da Web.

> 📌 **Esta página é só um ponteiro.** A documentação completa vive **no próprio repositório** —
> a wiki não duplica:
> - `README.md` — o produto: páginas, estrutura da planilha, setup
> - `CLAUDE.md` — regras de trabalho do assistente (commit/push sempre, nunca validar sozinho,
>   deploy via clasp, pendências) — **leitura obrigatória antes de mexer no código**
> - `CHANGELOG.md` — histórico das sessões de trabalho

## Localização e execução

| O quê | Onde |
|---|---|
| Código | `/root/finflow` |
| Repo GitHub | `omgiova/finflow` (privado; push via SSH com a chave da VPS) |
| Servidor dev | porta **3777**, sempre ligado (`npm run dev -- -p 3777`); Giovani testa ao vivo |
| Ver no celular | túnel de porta — ver [[wiki/systems/vps.md|VPS]] § Acesso SSH |
| Apps Script | deploy via clasp direto da VPS — comandos no `CLAUDE.md` do repo |

## Stack

Next.js (App Router) · Tailwind CSS · Recharts · motion · SWR · lucide-react

## Regra de trabalho especial

Neste projeto o assistente **não mexe na wiki** durante as sessões de trabalho — a documentação
viva fica no repo. Esta página só registra a existência do projeto e aponta pra lá.
