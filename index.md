---
okf_version: "0.1"
---

# wiki

Catálogo completo. Leia este arquivo primeiro ao responder queries — use os links para navegar às páginas relevantes.

## Estrutura

> Esta seção é a fonte de verdade da estrutura da wiki — deve estar sempre sincronizada com `git ls-files`. Ao criar, renomear ou remover qualquer arquivo ou pasta, atualize aqui.

```
.gitignore
.obsidian/
│   ├── community-plugins.json
│   ├── core-plugins.json
│   └── plugins/obsidian-git/
│       ├── main.js
│       ├── manifest.json
│       └── styles.css
AGENTS.md
index.md
log.md
├── raw/
│   ├── google-okf/
│   │   ├── google-okf-README.md
│   │   ├── introducing-the-open-knowledge-format.md
│   │   └── SPEC.md
│   ├── agents-cli-README.md
│   ├── akshay-pachaar-karpathy-agentic-engineering-tooling.md
│   ├── fluxo-2-trello-prazos-workflow-2026-07-09.md
│   ├── fluxo-3-om-database-workflow-2026-07-12.md
│   ├── karpathy-llm-wiki-pattern.md
│   └── libertas.md
└── wiki/
    ├── concepts/
    │   ├── mcps.md
    │   ├── okf.md
    │   ├── orquestrador.md
    │   ├── plano-implementacao-loop.md
    │   └── wiki.md
    ├── diario/
    │   ├── 2026-06-28-2012-20260628_195.md
    │   └── 2026-06-28-2155-20260628_212.md
    ├── diary/
    ├── history/
    │   ├── 2026-06-22-modelos-nim-elevenlabs.md
    │   ├── 2026-06-24-20260624.md
    │   ├── 2026-07-13-primeiro-render-hyperframes.md
    │   └── crise-update.md
    ├── procedures/
    │   ├── auditor-wiki.md
    │   ├── auditor-wiki-evals.md
    │   ├── curador-wiki-historico.md
    │   ├── curador-wiki.md
    │   ├── sync-automatico-wiki.md
    │   └── wiki-review.md
    ├── projects/
    │   ├── automacao-videos.md
    │   ├── automacao-trello-open-midia.md
    │   └── finflow.md
    ├── systems/
    │   ├── evolution-api.md
    │   ├── hermes-endpoints.md
    │   ├── hermes-estado.md
    │   ├── hermes.md
    │   ├── n8n.md
    │   ├── termux-ssh-claude.md
    │   └── vps.md
    ├── todo/
    │   └── proximos-passos.md
    └── tools/
        ├── elevenlabs-mcp.md
        ├── firecrawl.md
        ├── hyperframes.md
        ├── n8n-mcp.md
        ├── remotion.md
        ├── obsidian-git.md
        ├── telegram.md
        ├── trello-mcp-oficial.md
        └── trello-mcp.md
```

## raiz/

- [[AGENTS.md|AGENTS.md]] — instruções obrigatórias para agentes que trabalham neste repositório; lido automaticamente por Claude Code e Codex CLI
- [[index.md|index.md]] — este arquivo; catálogo completo e ponto de entrada único da wiki
- [[log.md|log.md]] — changelog estrutural da wiki; registro cronológico de criações, atualizações, renomeações e deleções de páginas

## wiki/

### systems/

- [[wiki/systems/hermes.md|Hermes]] — sistema principal: identidade, stack, modelos, interface REST e operação do Hermes Agent
- [[wiki/systems/hermes-endpoints.md|Hermes API]] — referência completa dos ~180 endpoints REST do Hermes Agent (gerada do /openapi.json)
- [[wiki/systems/hermes-estado.md|Hermes — Estado das Configurações]] — snapshot vivo de MCPs, skills, webhooks, toolsets e modelo (gerado automaticamente)
- [[wiki/systems/vps.md|VPS]] — Hostinger KVM 2 — hardware, serviços rodando, Docker Swarm, IPVS, problemas conhecidos
- [[wiki/systems/n8n.md|n8n]] — plataforma de automação: Swarm queue mode via EasyPanel, workflows, API/MCP, credenciais e erros conhecidos
- [[wiki/systems/evolution-api.md|Evolution API]] — gateway de WhatsApp na VPS (instância Giobot); canal de saída das automações do n8n
- [[wiki/systems/termux-ssh-claude.md|Termux + SSH + Claude]] — setup completo de acesso remoto: configuração, erros conhecidos e troubleshooting

### projects/

Repositórios criados pelo Giovani (dashboards, sites, jogos...). Cada página é um **ponteiro
enxuto** — o que é, onde mora, como roda — apontando pra documentação completa no próprio repo
(a wiki não duplica). Critério: *construí e é um produto* → `projects/`; *roda como serviço e
sustenta a infra* → `systems/`; *de terceiro, eu só uso* → `tools/`.

- [[wiki/projects/automacao-trello-open-midia.md|Automação Trello — Open Mídia]] — fluxos n8n sobre o board DEMANDAS GERAIS; fluxo 1 (aviso WhatsApp de membro adicionado a card, ativo desde 2026-07-06) e fluxo 2 (lista semanal de prazos, em produção desde 2026-07-08, com Error Workflow apontado)
- [[wiki/projects/finflow.md|finflow]] — dashboard de gestão financeira pessoal (Next.js + Planilha Google) em /root/finflow; dev na porta 3777; docs completas no repo (README/CLAUDE/CHANGELOG)
- [[wiki/projects/automacao-videos.md|Automação Vídeos]] — projeto de automação de vídeos do Giovani, em definição; meta é geração totalmente automática usando Remotion e/ou HyperFrames

### tools/

- [[wiki/tools/telegram.md|Telegram]] — referência completa: IDs, tópicos, HERMES_SESSION_MESSAGE_ID, sendMessage, sendRichMessage, reações e integrações com o Hermes
- [[wiki/tools/elevenlabs-mcp.md|ElevenLabs MCP]] — capabilities e limites do free tier: text_to_sound_effects, compose_music (pago), limitação de duração, créditos
- [[wiki/tools/n8n-mcp.md|n8n MCP]] — interface MCP do n8n para os agentes da VPS: server stdio, registro nos clientes, env, capabilities e erros conhecidos
- [[wiki/tools/remotion.md|Remotion]] — framework React para vídeos programáticos; projeto único em /root/projects/remotion, skills oficiais, referência dos projetos do PC em referencia-pc/, whisper fica no PC
- [[wiki/tools/hyperframes.md|HyperFrames]] — CLI open-source da HeyGen (HTML→MP4, feita pra agentes); instalada globalmente na VPS, ainda sem uso real
- [[wiki/tools/obsidian-git.md|Obsidian Git]] — todos os problemas já encontrados com o plugin obsidian-git, causas raiz e soluções definitivas
- [[wiki/tools/firecrawl.md|Firecrawl]] — busca com sintaxe site: para plataformas específicas; quando usar e não usar
- [[wiki/tools/trello-mcp.md|Trello MCP (comunidade)]] — MCP local via npx com credenciais da dona do workspace; wrapper em /root/mcp/, skill em /root/.hermes/skills/trello/; credenciais validadas 2026-07-06, registrado no Claude Code e no Hermes
- [[wiki/tools/trello-mcp-oficial.md|Trello MCP (oficial)]] — MCP na nuvem da Atlassian via OAuth; registrado no Claude Code mas bloqueado (Giovani é só convidado no board, OAuth exige membro do workspace)

### procedures/

- [[wiki/procedures/auditor-wiki.md|Auditor da Wiki]] — auditoria completa multi-agente em 3 fases: estrutural, semântica e coordenação; entrega página de findings priorizada
- [[wiki/procedures/auditor-wiki-evals.md|Auditor da Wiki — Evals e Validação]] — diagnóstico da primeira execução (desastre 2026-06-28), lições do agents-cli e Karpathy, e gates de validação obrigatórios antes de qualquer run
- [[wiki/procedures/curador-wiki.md|Curador da Wiki]] — papel, comportamento, arquitetura atual (v6/v5), formato de output e como executar
- [[wiki/procedures/curador-wiki-historico.md|Curador da Wiki — Histórico]] — registro completo de todas as tentativas, scripts e decisões de design desde a v1
- [[wiki/procedures/wiki-review.md|Wiki Review]] — agente background que roda a cada 10 turnos e salva insights no diary/
- [[wiki/procedures/sync-automatico-wiki.md|Sync automático da wiki (pull)]] — cron a cada 5min que puxa a wiki do GitHub, convergindo edições feitas em outros devices

### concepts/

- [[wiki/concepts/wiki.md|Wiki]] — o que é esta wiki, como funciona, regras de escrita e histórico de fundação
- [[wiki/concepts/okf.md|Open Knowledge Format (OKF)]] — padrão open source do Google Cloud para wikis de agentes; nossa wiki já é conformante; repo oficial para consulta futura
- [[wiki/concepts/plano-implementacao-loop.md|Plano de Implementação — Loops Agênticos]] — base de conhecimento e planejamento para construção de sistema de loop autônomo; fundamentado em fontes primárias (Steinberger, Boris Cherny, Addy Osmani, OpenClaw)
- [[wiki/concepts/orquestrador.md|Orquestrador da Memória]] — auditoria completa da wiki: OKF, estrutura, diário, automação, coordenação de agentes e roadmap de melhorias
- [[wiki/concepts/mcps.md|Registro central de MCPs]] — registro oficial dos MCPs da VPS: link para a página de cada um e em quais agentes está registrado
- [[wiki/concepts/automacao-principios.md|Princípios de Automação e Testes]] — regras gerais para construção, validação e maturação de automações multi-agente (Karpathy, evals, observabilidade, fail-fast)

### history/

- [[wiki/history/2026-07-13-primeiro-render-hyperframes.md|Primeiro render HyperFrames]] — primeiro vídeo real renderizado (projeto estilos-animacoes, 72s, 12 estilos de motion com nome técnico + parâmetros na tela); descoberta do requisito seek-safe (set + fromTo, não .from empilhado)
- [[wiki/history/2026-06-24-20260624.md|Diário 2026-06-24]] — daily longa (64KB); sessão com ElevenLabs SFX, agent loops, OpenClaw, escrita concorrente entre instâncias
- [[wiki/history/crise-update.md|Crise update]] — múltiplos /update corromperam state.db; backup salvou; fixes aplicados
- [[wiki/history/2026-06-22-modelos-nim-elevenlabs.md|Modelos NIM + ElevenLabs]] — migração para Nvidia NIM, rate limit agêntico, MCP ElevenLabs, tentativa Groq
- [[wiki/history/2026-06-28-libertas-carrossel-7sweeps.md|Revisão Libertas — 7 Sweeps]] — aplicação dos 7 Sweeps do conversion-copywriting no carrossel Libertas v2/v3, 10 intervenções documentadas contra o guia de voz

### todo/

- [[wiki/todo/proximos-passos.md|Próximos passos]] — to-do list ativa da wiki
- [[wiki/todo/violacoes-agentes.md|Violações de Regras — Agentes]] — registro append-only de violações graves de regras do AGENTS.md cometidas por agentes

### diario/

Inbox da wiki — captura bruta automática por sessão (wiki-review). Contém preferências reveladas, correções de comportamento, técnicas descobertas e pendências abertas. Não é destino final — insights estáveis devem ser extraídos e integrados em páginas permanentes.

- [[wiki/diario/2026-06-28-2012-20260628_195.md|Diário 2026-06-28 (20:12)]] — estrutura wiki, auditor v1 desastre (75% cota), decisões de taxonomia
- [[wiki/diario/2026-06-28-2155-20260628_212.md|Diário 2026-06-28 (21:55)]] — carrossel: regras de naming, fluxo narrativo, CTA personalizado, tela de fechamento

## raw/

### google-okf/

- [[raw/google-okf/google-okf-README.md|OKF README.md]] — intro e agente de referência do repo okf/
- [[raw/google-okf/SPEC.md|OKF SPEC.md]] — especificação oficial OKF v0.1 do Google Cloud
- [[raw/google-okf/introducing-the-open-knowledge-format.md|Introducing the Open Knowledge Format]] — blog post do Google Cloud (jun/2026)

### raiz
- [[raw/karpathy-llm-wiki-pattern.md|LLM Wiki Pattern (Karpathy)]] — documento original do padrão que esta wiki segue; fonte imutável
- [[raw/libertas.md|Guia de Voz — Libertas Assessoria Financeira]] — documento de referência para copywriter com tom de voz, exemplos aprovados e estrutura de conteúdo
- [[raw/fluxo-2-trello-prazos-workflow-2026-07-09.md|JSON do workflow "Trello Prazos por Membro" (v1 oficial, 2026-07-09)]] — export completo da primeira versão validada e em produção do Fluxo 2
- [[raw/fluxo-3-om-database-workflow-2026-07-12.md|JSON do workflow "Trello Open Mídia - Banco de Dados" (v1 validada, 2026-07-12)]] — export completo da v1 imutável do Fluxo 3; derivadas devem duplicar o flow, não editar
- [[raw/akshay-pachaar-karpathy-agentic-engineering-tooling.md|Karpathy's Agentic Engineering Finally Has Proper Tooling]] — artigo de Akshay Pachaar sobre Google Agents CLI (jun/2026)
- [[raw/agents-cli-README.md|Google Agents CLI — README oficial]] — README do repositório google/agents-cli
