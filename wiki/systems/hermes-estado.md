---
type: system
tags: [hermes, configuracao, estado]
title: Hermes Agent — Estado das Configurações
description: Estado atual das integrações do Hermes (MCPs, skills, webhooks, toolsets, modelos) — gerado automaticamente, seção Interface do sistema Hermes
timestamp: 2026-07-08T22:19:06-03:00
status: stable
---

# Hermes Agent — Estado das Configurações

> **Gerado automaticamente.** Para atualizar manualmente: execute `/root/scripts/update-hermes-wiki.sh`
> Última atualização: 2026-07-08T22:19:06-03:00

## Status geral

- **Gateway:** ?
- **Modelo principal:** `deepseek-v4-flash` (deepseek)

## Servidores MCP

_(erro ao processar: Expecting value: line 1 column 1 (char 0))_

## Skills

- `ad-creative` ✓ — When the user wants to generate, iterate, or scale ad creative — headlines, descriptions, primary text, or full ad variations — for any paid advertising platform. Also use when the user mentions 'ad copy variations,' 'ad creative,' 'generate headlines,' 'RSA headlines,' 'bulk ad copy,' 'ad iterations,' 'creative testing,' 'ad performance optimization,' 'write me some ads,' 'Facebook ad copy,' 'Google ad headlines,' 'LinkedIn ad text,' or 'I need more ad variations.' Use this whenever someone needs to produce ad copy at scale or iterate on existing ads. For campaign strategy and targeting, see ads. For landing page copy, see copywriting.
- `ads-copywriter` ✓ — Multi-platform ad copy generation for Google Ads, Meta/Facebook, TikTok, LinkedIn with A/B testing variants
- `ads` ✓ — When the user wants help with paid advertising campaigns on Google Ads, Meta (Facebook/Instagram), LinkedIn, Twitter/X, or other ad platforms. Also use when the user mentions 'PPC,' 'paid media,' 'ROAS,' 'CPA,' 'ad campaign,' 'retargeting,' 'audience targeting,' 'Google Ads,' 'Facebook ads,' 'LinkedIn ads,' 'ad budget,' 'cost per click,' 'ad spend,' or 'should I run ads.' Use this for campaign strategy, audience targeting, bidding, and optimization. For bulk ad creative generation and iteration, see ad-creative. For landing page optimization, see cro.
- `ai-social-media-content` ✓ — Create AI-powered social media content for TikTok, Instagram, YouTube, Twitter/X. Generate: images, videos, reels, shorts, thumbnails, captions, hashtags. Tools: FLUX, Veo, Seedance, Wan, Kokoro TTS, Claude for copywriting. Use for: content creators, social media managers, influencers, brands. Triggers: social media content, tiktok, instagram reels, youtube shorts, twitter post, content creator, ai influencer, social content, reels, shorts, viral content, thumbnail generator, caption generator, hashtag generator, ugc content
- `alexa-notifications` ✓ — Send notifications to Alexa via NotifyMe through Node-RED /lembretes endpoint. Use when the user wants to schedule a reminder or send a spoken notification to Alexa.
- `analytics` ✓ — When the user wants to set up, improve, or audit analytics tracking and measurement. Also use when the user mentions "set up tracking," "GA4," "Google Analytics," "conversion tracking," "event tracking," "UTM parameters," "tag manager," "GTM," "analytics implementation," "tracking plan," "how do I measure this," "track conversions," "attribution," "Mixpanel," "Segment," "are my events firing," or "analytics isn't working." Use this whenever someone asks how to know if something is working or wants to measure marketing results. For A/B test measurement, see ab-testing.
- `claude-code` ✓ — Delegate coding to Claude Code CLI (features, PRs).
- `codex` ✓ — Delegate coding to OpenAI Codex CLI (features, PRs).
- `hermes-agent` ✓ — Configure, extend, or contribute to Hermes Agent.
- `opencode` ✓ — Delegate coding to OpenCode CLI (features, PR review).
- `brand-architect` ✓ — Use this skill when users need to develop brand strategy, choose a company name, define brand positioning, create brand voice, or build brand identity from day one. Activates for "what should I name it," "brand strategy," "positioning," or identity questions.
- `brand-copywriter` ✓ — Writes marketing copy using proven copywriting frameworks. Use when user needs copy for ads (Facebook, Instagram, TikTok, YouTube), landing pages, sales pages, email sequences, LinkedIn posts, product descriptions, or any marketing content.
- `brand-storytelling` ✓ — Help users craft compelling brand narratives. Use when someone is defining brand strategy, writing company positioning, creating pitch narratives, developing messaging frameworks, or trying to make their company story more memorable.
- `caption-writer-sms` ✓ — When the user wants to write a caption for a visual-first social media post on Facebook, Instagram, TikTok, Pinterest, or YouTube. Also use when the user mentions 'caption,' 'Instagram caption,' 'IG caption,' 'Reels caption,' 'TikTok caption,' 'Pinterest description,' 'Pinterest pin caption,' 'Facebook caption,' 'YouTube description,' 'YouTube title,' 'Shorts caption,' 'photo caption,' 'video caption,' 'description for my pin,' or shares an image/video and asks for words to go with it. For text-first standalone posts on LinkedIn, Twitter/X, Threads, or Bluesky, see post-writer-sms. For multi-slide carousels, see carousel-writer-sms. For opening lines, see hook-writer-sms.
- `carousel-writer-sms` ✓ — When the user wants to write content for a LinkedIn carousel, Instagram carousel, Facebook carousel, TikTok photo carousel, Pinterest Idea Pin, or any swipeable multi-slide format. Also use when the user mentions 'carousel,' 'slides,' 'LinkedIn carousel,' 'Instagram carousel,' 'IG carousel,' 'photo carousel,' 'TikTok photo carousel,' 'Idea Pin,' 'Pinterest Idea Pin,' 'swipe post,' 'slide deck,' or 'visual content.' Outputs slide-by-slide text content (not visual design). For single posts, see post-writer-sms. For threads, see thread-writer-sms. For caption copy under each slide post, see caption-writer-sms.
- `computer-use` ✓ — Drive the user's desktop in the background — clicking, typing,
scrolling, dragging — without stealing the cursor, keyboard focus,
or switching virtual desktops / Spaces. Cross-platform: macOS,
Windows, Linux. Works with any tool-capable model. Load this skill
whenever the `computer_use` tool is available.

- `content-strategy-sms` ✓ — When the user wants to plan a social media content strategy, decide what to post, or figure out topic clusters and content mix. Also use when the user mentions 'content strategy,' 'what should I post,' 'content ideas,' 'topic clusters,' 'content pillars,' 'content planning,' 'content mix,' 'I don't know what to post,' or 'social media strategy.' Use this to define the what and why of posting. For writing actual posts, see post-writer-sms. For scheduling, see content-calendar-sms. For platform-specific tactics, see platform-strategy-sms.
- `content-strategy` ✓ — When the user wants to plan a content strategy, decide what content to create, or figure out what topics to cover. Also use when the user mentions "content strategy," "what should I write about," "content ideas," "blog strategy," "topic clusters," "content planning," "editorial calendar," "content marketing," "content roadmap," "what content should I create," "blog topics," "content pillars," or "I don't know what to write." Use this whenever someone needs help deciding what content to produce, not just writing it. For writing individual pieces, see copywriting. For SEO-specific audits, see seo-audit. For social media content specifically, see social.
- `conversion-copywriting` ✓ — Write copy that gets a "yes" using Joanna Wiebe's research-first, Voice of Customer methodology Use when: **Writing landing pages, emails, or sales pages** that need measurable conversion results; **Starting a new copy project** and need a systematic process to follow; **Struggling with what to write** and staring at a blank page; **Wanting to prove ROI** to clients with data-backed decisions; **Improving existing copy** through validation and testing
- `copywriting` ✓ — When the user wants to write, rewrite, or improve marketing copy for any page — including homepage, landing pages, pricing pages, feature pages, about pages, or product pages. Also use when the user says "write copy for," "improve this copy," "rewrite this page," "marketing copy," "headline help," "CTA copy," "value proposition," "tagline," "subheadline," "hero section copy," "above the fold," "this copy is weak," "make this more compelling," or "help me describe my product." Use this whenever someone is working on website text that needs to persuade or convert. For email copy, see emails. For popup copy, see popups. For editing existing copy, see copy-editing.
- `architecture-diagram` ✓ — Dark-themed SVG architecture/cloud/infra diagrams as HTML.
- `ascii-art` ✓ — ASCII art: pyfiglet, cowsay, boxes, image-to-ascii.
- `ascii-video` ✓ — ASCII video: convert video/audio to colored ASCII MP4/GIF.
- `baoyu-article-illustrator` ✓ — Article illustrations: type × style × palette consistency.
- `baoyu-comic` ✓ — Knowledge comics (知识漫画): educational, biography, tutorial.
- `baoyu-infographic` ✓ — Infographics: 21 layouts x 21 styles (信息图, 可视化).
- `claude-design` ✓ — Design one-off HTML artifacts (landing, deck, prototype).
- `comfyui` ✓ — Generate images, video, and audio with ComfyUI — install, launch, manage nodes/models, run workflows with parameter injection. Uses the official comfy-cli for lifecycle and direct REST/WebSocket API for execution.
- `ideation` ✓ — Generate project ideas via creative constraints.
- `design-md` ✓ — Author/validate/export Google's DESIGN.md token spec files.
- `excalidraw` ✓ — Hand-drawn Excalidraw JSON diagrams (arch, flow, seq).
- `humanizer` ✓ — Humanize text: strip AI-isms and add real voice.
- `manim-video` ✓ — Manim CE animations: 3Blue1Brown math/algo videos.
- `p5js` ✓ — p5.js sketches: gen art, shaders, interactive, 3D.
- `pixel-art` ✓ — Pixel art w/ era palettes (NES, Game Boy, PICO-8).
- `popular-web-designs` ✓ — 54 real design systems (Stripe, Linear, Vercel) as HTML/CSS.
- `pretext` ✓ — Use when building creative browser demos with @chenglou/pretext — DOM-free text layout for ASCII art, typographic flow around obstacles, text-as-geometry games, kinetic typography, and text-powered generative art. Produces single-file HTML demos by default.
- `sketch` ✓ — Throwaway HTML mockups: 2-3 design variants to compare.
- `songwriting-and-ai-music` ✓ — Songwriting craft and Suno AI music prompts.
- `touchdesigner-mcp` ✓ — Control a running TouchDesigner instance via twozero MCP — create operators, set parameters, wire connections, execute Python, build real-time visuals. 36 native tools.
- `cron-news-scraping` ✓ — Set up and maintain scheduled news-scraping cron jobs with firecrawl CLI. Covers the full pipeline: search, language filter, dedup, webhook POST, silent delivery.
- `cron-prompt-patterns` ✓ — Define and maintain standard prompt patterns for cron jobs in Hermes. Covers multi-action patterns, pronunciation rules, one-shot scheduling, and cron job formatting.
- `jupyter-live-kernel` ✓ — Iterative Python via live Jupyter kernel (hamelnb).
- `ai-memory` ✓ — Deploy, configure, and integrate the ai-memory MCP server — LLM providers, project management, wiki management, and Obsidian vault sync.
- `hermes-maintenance` ✓ — Safely update Hermes Agent, back up user data pre-update, and recover from update failures that wipe untracked user files (SOUL.md, USER.md, MEMORY.md, custom skills, state.db).
- `llm-api-cost-tracking` ✓ — Track, log, and visualize LLM API spending across providers. Covers local logging from API responses, platform CSV export, and proxy-based approaches. Provider-specific quirks in references/.
- `system-modification-protocol` ✓ — Protocol for setup, configuration, and system-modification tasks.
Governs how to approach, communicate, and execute changes to the VPS.
- `user-interaction-protocol` ✓ — Governs how the agent interacts with Giovani across ALL contexts.
Covers communication style, data sourcing, session startup, knowledge storage,
and correction handling. Broader than system-modification-protocol (system tasks only).
- `vps-service-deployment` ✓ — Deploy and expose services via Docker (Swarm) + EasyPanel + Traefik on the Hostinger KVM 2 VPS.
- `docker-host-interaction-troubleshooting` ✓ — Troubleshoot and resolve configuration issues related to Docker host interactions, container networking, volume mounts, and permission problems.
- `dogfood` ✓ — Exploratory QA of web apps: find bugs, evidence, reports.
- `himalaya` ✓ — Himalaya CLI: IMAP/SMTP email from terminal.
- `firecrawl` ✓ — Search, scrape, and interact with the web via the Firecrawl CLI. Use this skill whenever the user wants to search the web, find articles, research a topic, look something up online, scrape a webpage, grab content from a URL, get data from a website, crawl documentation, download a site, or interact with pages that need clicks or logins.

- `minecraft-modpack-server` ✓ — Host modded Minecraft servers (CurseForge, Modrinth).
- `pokemon-player` ✓ — Play Pokemon via headless emulator + RAM reads.
- `codebase-inspection` ✓ — Inspect codebases w/ pygount: LOC, languages, ratios.
- `github-auth` ✓ — GitHub auth setup: HTTPS tokens, SSH keys, gh CLI login.
- `github-code-review` ✓ — Review PRs: diffs, inline comments via gh or REST.
- `github-issues` ✓ — Create, triage, label, assign GitHub issues via gh or REST.
- `github-pr-workflow` ✓ — GitHub PR lifecycle: branch, commit, open, CI, merge.
- `github-repo-management` ✓ — Clone/create/fork repos; manage remotes, releases.
- `github-token-masking` ✓ — Como extrair tokens do .env sem o mascaramento do Hermes bloquear o valor — técnica de hex-encoding para uso em URLs de remote git.
- `hermes-gateway-tool-configuration` ✓ — Configure Hermes Gateway toolsets, plugins, Docker mounts, and credentials — covers the full path from 'tool not working in gateway' to 'tool working in Telegram/Discord'. Use when the user reports a tool that works in CLI but not in Hermes Gateway (Telegram/Discord), or needs to set up a new integration in the gateway.

- `hook-writer-sms` ✓ — When the user wants help writing opening lines, hooks, first sentences, video hooks, thumbnails titles, or pin titles that grab attention. Also use when the user mentions 'hook,' 'opening line,' 'first line,' 'scroll stopper,' 'attention grabber,' 'headline,' 'video hook,' 'on-screen hook,' 'YouTube title,' 'thumbnail text,' 'pin title,' 'how to start my post,' or 'nobody reads past my first line.' Covers text-first platforms (LinkedIn, Twitter/X, Threads, Bluesky) and visual-first platforms (Facebook, Instagram, TikTok, Pinterest, YouTube). Can be used standalone or invoked by other creation skills. For writing full posts, see post-writer-sms. For threads, see thread-writer-sms.
- `marketing-psychology` ✓ — When the user wants to apply psychological principles, mental models, or behavioral science to marketing. Also use when the user mentions 'psychology,' 'mental models,' 'cognitive bias,' 'persuasion,' 'behavioral science,' 'why people buy,' 'decision-making,' 'consumer behavior,' 'anchoring,' 'social proof,' 'scarcity,' 'loss aversion,' 'framing,' or 'nudge.' Use this whenever someone wants to understand or leverage how people think and make decisions in a marketing context. For applying psychology to specific pages, see cro; for pricing tactics, see pricing; for copy framing, see copywriting.
- `elevenlabs-sfx` ✓ — Generate sound effects via ElevenLabs text_to_sound_effects MCP tool — text descriptions to audio files.
- `gif-search` ✓ — Search/download GIFs from Tenor via curl + jq.
- `heartmula` ✓ — HeartMuLa: Suno-like song generation from lyrics + tags.
- `songsee` ✓ — Audio spectrograms/features (mel, chroma, MFCC) via CLI.
- `youtube-content` ✓ — YouTube transcripts to summaries, threads, blogs.
- `evaluating-llms-harness` ✓ — lm-eval-harness: benchmark LLMs (MMLU, GSM8K, etc.).
- `weights-and-biases` ✓ — W&B: log ML experiments, sweeps, model registry, dashboards.
- `huggingface-hub` ✓ — HuggingFace hf CLI: search/download/upload models, datasets.
- `llama-cpp` ✓ — llama.cpp local GGUF inference + HF Hub model discovery.
- `serving-llms-vllm` ✓ — vLLM: high-throughput LLM serving, OpenAI API, quantization.
- `audiocraft-audio-generation` ✓ — AudioCraft: MusicGen text-to-music, AudioGen text-to-sound.
- `segment-anything-model` ✓ — SAM: zero-shot image segmentation via points, boxes, masks.
- `dspy` ✓ — DSPy: declarative LM programs, auto-optimize prompts, RAG.
- `n8n-code-javascript` ✓ — Write JavaScript code in n8n Code nodes. Use when writing JavaScript in n8n, using $input/$json/$node syntax, making HTTP requests with $helpers, working with dates using DateTime, troubleshooting Code node errors, choosing between Code node modes, or doing any custom data transformation in n8n. Always use this skill when a workflow needs a Code node — whether for data aggregation, filtering, API calls, format conversion, batch processing logic, or any custom JavaScript. Covers SplitInBatches loop patterns, cross-iteration data, pairedItem, and real-world production patterns. EXCEPTION — for the AI-agent-callable Custom Code Tool (@n8n/n8n-nodes-langchain.toolCode, a tool attached to an AI Agent), use the n8n-code-tool skill instead; it has a different runtime contract.
- `n8n-code-python` ✓ — Write Python code in n8n Code nodes. Use when writing Python in n8n, using _input/_json/_node syntax, working with standard library, or need to understand Python limitations in n8n Code nodes. Use this skill when the user specifically requests Python for an n8n Code node. Note — JavaScript is recommended for 95% of use cases — only use Python when the user explicitly prefers it or the task requires Python-specific standard library capabilities (regex, hashlib, statistics). EXCEPTION — for Python in the AI-agent-callable Custom Code Tool (@n8n/n8n-nodes-langchain.toolCode), use the n8n-code-tool skill instead (input is _query, return must be a string).
- `n8n-expression-syntax` ✓ — Validate n8n expression syntax and fix common errors. Use when writing n8n expressions, using {{}} syntax, accessing $json/$node variables, troubleshooting expression errors, mapping data between nodes, or referencing webhook data in workflows. Use this skill whenever configuring node fields that reference data from previous nodes — expressions are how n8n passes data between nodes, and getting the syntax wrong is the most common source of workflow errors.
- `n8n-mcp-tools-expert` ✓ — Expert guide for using n8n-mcp MCP tools effectively. Use when searching for nodes, validating configurations, accessing templates, managing workflows, managing credentials, auditing instance security, or using any n8n-mcp tool. Provides tool selection guidance, parameter formats, and common patterns. IMPORTANT — Always consult this skill before calling any n8n-mcp tool — it prevents common mistakes like wrong nodeType formats, incorrect parameter structures, and inefficient tool usage. If the user mentions n8n, workflows, nodes, or automation and you have n8n MCP tools available, use this skill first.
- `n8n-node-configuration` ✓ — Operation-aware node configuration guidance. Use when configuring nodes, understanding property dependencies, determining required fields, choosing between get_node detail levels, or learning common configuration patterns by node type. Always use this skill when setting up node parameters — it explains which fields are required for each operation, how displayOptions control field visibility, and when to use patchNodeField for surgical edits vs full node updates.
- `n8n-validation-expert` ✓ — Interpret validation errors and guide fixing them. Use when encountering validation errors, validation warnings, false positives, operator structure issues, or need help understanding validation results. Also use when asking about validation profiles, error types, the validation loop process, or auto-fix capabilities. Consult this skill whenever a validate_node or validate_workflow call returns errors or warnings — it knows which warnings are false positives and which errors need real fixes.
- `n8n-workflow-builder` ✓ — Guia técnico para construir e editar workflows no n8n usando as MCP tools (create_workflow, update_workflow, get_workflow, list_workflows, execute_workflow, delete_workflow, export_workflow, import_workflow, activate_workflow, deactivate_workflow)
- `n8n-workflow-patterns` ✓ — Proven workflow architectural patterns from real n8n workflows. Use when building new workflows, designing workflow structure, choosing workflow patterns, planning workflow architecture, or asking about webhook processing, HTTP API integration, database operations, AI agent workflows, batch processing, or scheduled tasks. Always consult this skill when the user asks to create, build, or design an n8n workflow, automate a process, or connect services — even if they don't explicitly mention 'patterns'. Covers webhook, API, database, AI, batch processing, and scheduled automation architectures.
- `node-red-alexa-evolution` ✓ — Evolution roadmap for Node-RED + Alexa integration. Guides the implementation of a complete smart home and voice assistant pipeline: from basic smart home devices through custom Alexa skills with SSML, audio, and integrated automations.
- `obsidian-ai-memory` ✓ — Setup específico do usuário — ai-memory ↔ Obsidian vault sync, Obsidian Git plugin (Windows/Android), rebuild script, gotchas de autenticação e preferências de manutenção do vault.
- `obsidian-wiki-maintenance` ✓ — Enriquecer e manter vaults Obsidian com [[wikilinks]], INDEX.md, conexões entre projetos e sincronia via git. Scripts reutilizáveis cruzam tags do frontmatter pra conectar páginas relacionadas.
- `obsidian` ✓ — Read, search, create, and edit notes in the Obsidian vault.
- `airtable` ✓ — Airtable REST API via curl. Records CRUD, filters, upserts.
- `google-workspace` ✓ — Gmail, Calendar, Drive, Docs, Sheets via gws CLI or Python.
- `maps` ✓ — Geocode, POIs, routes, timezones via OpenStreetMap/OSRM.
- `nano-pdf` ✓ — Edit PDF text/typos/titles via nano-pdf CLI (NL prompts).
- `notion` ✓ — Notion API + ntn CLI: pages, databases, markdown, Workers.
- `ocr-and-documents` ✓ — Extract text from PDFs/scans (pymupdf, marker-pdf).
- `petdex` ✓ — Install and select animated petdex mascots for Hermes.
- `powerpoint` ✓ — Create, read, edit .pptx decks, slides, notes, templates.
- `teams-meeting-pipeline` ✓ — Operate the Teams meeting summary pipeline via Hermes CLI — summarize meetings, inspect pipeline status, replay jobs, manage Microsoft Graph subscriptions.
- `agent-memory-architecture` ✓ — Design agent memory systems combining manual knowledge bases (Obsidian vaults) with automatic capture tools (ai-memory). Covers vault structure, complementary layers, and integration patterns for cross-agent persistence.
- `arxiv` ✓ — Search arXiv papers by keyword, author, category, or ID.
- `blogwatcher` ✓ — Monitor blogs and RSS/Atom feeds via blogwatcher-cli tool.
- `llm-wiki-ai-memory` ✓ — Complemento ao llm-wiki para uso com ai-memory como backend — UUID folders, OKF principles, renomear arquivos em wikis git-backed, e deduplicação de conceitos.
- `llm-wiki` ✓ — Karpathy's LLM Wiki: build/query interlinked markdown KB.
- `polymarket` ✓ — Query Polymarket: markets, prices, orderbooks, history.
- `research-paper-writing` ✓ — Write ML papers for NeurIPS/ICML/ICLR: design→submit.
- `senior-react-video-developer` ✓ — Skill para desenvolvimento de videos com React e Remotion. Instalacao, componentes, manipulacao de midia, renderizacao e exportacao.
- `seo-audit` ✓ — When the user wants to audit, review, or diagnose SEO issues on their site. Also use when the user mentions "SEO audit," "technical SEO," "why am I not ranking," "SEO issues," "on-page SEO," "meta tags review," "SEO health check," "my traffic dropped," "lost rankings," "not showing up in Google," "site isn't ranking," "Google update hit me," "page speed," "core web vitals," "crawl errors," or "indexing issues." Use this even if the user just says something vague like "my SEO is bad" or "help with SEO" — start with an audit. For building pages at scale to target keywords, see programmatic-seo. For adding structured data, see schema. For AI search optimization, see ai-seo.
- `skill-catalog-format` ✓ — Template de formatação para skills no catálogo do Telegram no tópico Skills
- `openhue` ✓ — Control Philips Hue lights, scenes, rooms via OpenHue CLI.
- `social-content` ✓ — When the user wants help creating, scheduling, or optimizing social media content for LinkedIn, Twitter/X, Instagram, TikTok, Facebook, or other platforms. Also use when the user mentions 'LinkedIn post,' 'Twitter thread,' 'social media,' 'content calendar,' 'social scheduling,' 'engagement,' or 'viral content.' This skill covers content creation, repurposing, and platform-specific strategies.
- `xurl` ✓ — X/Twitter via xurl CLI: post, search, DM, media, v2 API.
- `social` ✓ — When the user wants help creating, scheduling, or optimizing social media content for LinkedIn, Twitter/X, Instagram, TikTok, Facebook, or other platforms, or wants to do social listening and engagement triage. Also use when the user mentions 'LinkedIn post,' 'Twitter thread,' 'social media,' 'content calendar,' 'social scheduling,' 'engagement,' 'viral content,' 'what should I post,' 'repurpose this content,' 'tweet ideas,' 'LinkedIn carousel,' 'social media strategy,' 'grow my following,' 'TikTok video,' 'Reels,' 'Shorts,' 'video script,' 'video hook,' 'short-form video,' 'create a reel,' 'social listening,' 'brand mentions,' 'competitor monitoring,' 'top posts to comment on,' or 'find people asking for.' Use this for social media content creation, repurposing, scheduling, short-form video scripting, and social listening. For broader content strategy, see content-strategy. For paid ads, see ad-creative. For earned media, see public-relations.
- `ai-memory-wiki` ✓ — Gerenciar vault wiki markdown com OKF + ai-memory — editar, commit, push, sincronizar SQLite
- `hermes-agent-skill-authoring` ✓ — Author in-repo SKILL.md: frontmatter, validator, structure, and writing-quality principles.
- `node-inspect-debugger` ✓ — Debug Node.js via --inspect + Chrome DevTools Protocol CLI.
- `plan` ✓ — Plan mode: write an actionable markdown plan to .hermes/plans/, no execution. Bite-sized tasks, exact paths, complete code.
- `python-debugpy` ✓ — Debug Python: pdb REPL + debugpy remote (DAP).
- `requesting-code-review` ✓ — Pre-commit review: security scan, quality gates, auto-fix.
- `simplify-code` ✓ — Parallel 3-agent cleanup of recent code changes.
- `spike` ✓ — Throwaway experiments to validate an idea before build.
- `systematic-debugging` ✓ — 4-phase root cause debugging: understand bugs before fixing.
- `test-driven-development` ✓ — TDD: enforce RED-GREEN-REFACTOR, tests before code.
- `spotify-helper` ✓ — Skill para ajudar a controlar e gerenciar funcionalidades do Spotify via Hermes Agent, garantindo uma experiencia proativa e intuitiva.
- `telegram-rich-messages` ✓ — Complete reference for Telegram Rich Messages: Markdown and HTML formatting, sendRichMessage API, RichText types, limits, and best practices for sending formatted messages via Hermes.
- `telegram-topics` ✓ — Mapa completo dos chats, grupos e tópicos do Telegram do Giovani — IDs e referências para entrega de mensagens.
- `telegram-bot-api` ✓ — Call the Telegram Bot API directly from Hermes (reactions, custom methods, forum management).
Use when the user asks to react to messages, manage forum topics, use setMessageReaction,
or any Telegram Bot API method not exposed as a native Hermes tool. Covers authentication,
chat_id/message_id resolution via session context, and the reaction emoji whitelist.
- `telegram-reaction-safe` ✓ — Safe procedure for sending Telegram reactions with mandatory verification.
Use when the user asks to react to a specific message and requires certainty
that the reaction was applied to the correct message. Eliminates guesswork
by requiring validation before action and user confirmation after.
- `mcp-server-trello` ✓ — Trello MCP Server skill for board discovery, card workflows, checklist management, comments, attachments, labels, members, board/workspace selection, and health monitoring through the bundled @delorenj/mcp-server-trello server.
- `yuanbao` ✓ — Yuanbao (元宝) groups: @mention users, query info/members.

## Webhooks

- `?` ✗

## Toolsets

_(erro ao processar: Expecting value: line 1 column 1 (char 0))_

## Perfis

- `default` — deepseek-v4-flash

## Cron jobs

_(nenhum configurado)_

## Plataformas de mensagens

- `Telegram` ✓ — Run Hermes from Telegram DMs, groups, and topics.
- `Discord` ✗ — Connect Hermes to Discord DMs, channels, and threads.
- `Slack` ✗ — Use Hermes from Slack via Socket Mode. Add allowed Slack member IDs so connected bots can respond.
- `Mattermost` ✗ — Connect Hermes to Mattermost channels and direct messages.
- `Matrix` ✗ — Use Hermes in Matrix rooms and direct messages.
- `WhatsApp` ✗ — Use Hermes through the bundled WhatsApp bridge with QR-based auth.
- `Signal` ✗ — Connect through a signal-cli REST bridge.
- `BlueBubbles (iMessage)` ✗ — Use Hermes through iMessage via a BlueBubbles server.
- `Home Assistant` ✗ — Control your smart home from Hermes via Home Assistant.
- `Email` ✗ — Talk to Hermes through an IMAP/SMTP mailbox.
- `SMS (Twilio)` ✗ — Send and receive text messages via Twilio.
- `DingTalk` ✗ — Connect Hermes to DingTalk groups (钉钉).
- `Feishu / Lark` ✗ — Use Hermes inside Feishu / Lark.
- `Google Chat` ✗ — Connect Hermes to Google Chat via Cloud Pub/Sub.
- `WeCom (group bot)` ✗ — Send-only WeCom group bot via webhook.
- `WeCom (app)` ✗ — Two-way WeCom integration via callback app.
- `Weixin / WeChat (Personal)` ✗ — Connect a personal WeChat account through Tencent's iLink Bot API.
- `QQ Bot` ✗ — Connect Hermes to a QQ Bot from the QQ Open Platform.
- `Yuanbao (元宝)` ✗ — Connect Hermes to Tencent Yuanbao.
- `API server` ✗ — Expose Hermes as an OpenAI-compatible HTTP API for tools like Open WebUI.
- `Webhooks` ✗ — Receive events from GitHub, GitLab, and other webhook sources.
- `Irc` ✗
- `Line` ✗
- `Msgraph Webhook` ✗
- `Ntfy` ✗
- `Photon` ✗
- `Raft` ✗
- `Relay` ✗
- `Simplex` ✗
- `Teams` ✗
- `Whatsapp Cloud` ✗

---

## Conexões

- [[wiki/systems/hermes-endpoints.md]]
- [[wiki/systems/hermes.md]]
- [[wiki/systems/vps.md]]
