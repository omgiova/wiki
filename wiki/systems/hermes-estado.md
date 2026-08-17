---
type: system
tags: [hermes, configuracao, estado]
title: Hermes Agent — Estado das Configurações
description: Estado atual das integrações do Hermes (MCPs, skills, webhooks, toolsets, modelos) — gerado automaticamente, seção Interface do sistema Hermes
timestamp: 2026-08-17T03:00:01-03:00
status: stable
---

# Hermes Agent — Estado das Configurações

> **Gerado automaticamente.** Para atualizar manualmente: execute `/root/scripts/update-hermes-wiki.sh`
> Última atualização: 2026-08-17T03:00:01-03:00

## Status geral

- **Gateway:** ?
- **Modelo principal:** `deepseek-v4-flash` (deepseek)

## Servidores MCP

- `ai-memory` ✗
- `elevenlabs` ✓
- `n8n` ✓
- `trello` ✓

## Skills

- `ad-creative` ✓ — When the user wants to generate, iterate, or scale ad creative — headlines, descriptions, primary text, or full ad variations — for any paid advertising platform. Also use when the user mentions 'ad copy variations,' 'ad creative,' 'generate headlines,' 'RSA headlines,' 'bulk ad copy,' 'ad iterations,' 'creative testing,' 'ad performance optimization,' 'write me some ads,' 'Facebook ad copy,' 'Google ad headlines,' 'LinkedIn ad text,' or 'I need more ad variations.' Use this whenever someone needs to produce ad copy at scale or iterate on existing ads. For campaign strategy and targeting, see ads. For landing page copy, see copywriting.
- `ads-copywriter` ✓ — Multi-platform ad copy generation for Google Ads, Meta/Facebook, TikTok, LinkedIn with A/B testing variants
- `ads` ✓ — When the user wants help with paid advertising campaigns on Google Ads, Meta (Facebook/Instagram), LinkedIn, Twitter/X, or other ad platforms. Also use when the user mentions 'PPC,' 'paid media,' 'ROAS,' 'CPA,' 'ad campaign,' 'retargeting,' 'audience targeting,' 'Google Ads,' 'Facebook ads,' 'LinkedIn ads,' 'ad budget,' 'cost per click,' 'ad spend,' or 'should I run ads.' Use this for campaign strategy, audience targeting, bidding, and optimization. For bulk ad creative generation and iteration, see ad-creative. For landing page optimization, see cro.
- `ai-social-media-content` ✓ — Create AI-powered social media content for TikTok, Instagram, YouTube, Twitter/X. Generate: images, videos, reels, shorts, thumbnails, captions, hashtags. Tools: FLUX, Veo, Seedance, Wan, Kokoro TTS, Claude for copywriting. Use for: content creators, social media managers, influencers, brands. Triggers: social media content, tiktok, instagram reels, youtube shorts, twitter post, content creator, ai influencer, social content, reels, shorts, viral content, thumbnail generator, caption generator, hashtag generator, ugc content
- `alexa-notifications` ✓ — Send notifications to Alexa via NotifyMe through Node-RED /lembretes endpoint. Use when the user wants to schedule a reminder or send a spoken notification to Alexa.
- `analytics` ✓ — When the user wants to set up, improve, or audit analytics tracking and measurement. Also use when the user mentions "set up tracking," "GA4," "Google Analytics," "conversion tracking," "event tracking," "UTM parameters," "tag manager," "GTM," "analytics implementation," "tracking plan," "how do I measure this," "track conversions," "attribution," "Mixpanel," "Segment," "are my events firing," or "analytics isn't working." Use this whenever someone asks how to know if something is working or wants to measure marketing results. For A/B test measurement, see ab-testing.
- `claude-code` ✓ — Delegate coding to Claude Code CLI (features, PRs).
- `codex` ✓ — Delegate coding to OpenAI Codex CLI (features, PRs).
- `computer-use` ✓ — Drive the desktop in the background without stealing focus.
- `hermes-agent` ✓ — Use, configure, theme, extend, and orchestrate Hermes Agent.
- `merge-reconciler` ✓ — Neutral third-party resolution of agent merge conflicts.
- `opencode` ✓ — Delegate coding to OpenCode CLI (features, PR review).
- `brand-architect` ✓ — Use this skill when users need to develop brand strategy, choose a company name, define brand positioning, create brand voice, or build brand identity from day one. Activates for "what should I name it," "brand strategy," "positioning," or identity questions.
- `brand-copywriter` ✓ — Writes marketing copy using proven copywriting frameworks. Use when user needs copy for ads (Facebook, Instagram, TikTok, YouTube), landing pages, sales pages, email sequences, LinkedIn posts, product descriptions, or any marketing content.
- `brand-storytelling` ✓ — Help users craft compelling brand narratives. Use when someone is defining brand strategy, writing company positioning, creating pitch narratives, developing messaging frameworks, or trying to make their company story more memorable.
- `caption-writer-sms` ✓ — When the user wants to write a caption for a visual-first social media post on Facebook, Instagram, TikTok, Pinterest, or YouTube. Also use when the user mentions 'caption,' 'Instagram caption,' 'IG caption,' 'Reels caption,' 'TikTok caption,' 'Pinterest description,' 'Pinterest pin caption,' 'Facebook caption,' 'YouTube description,' 'YouTube title,' 'Shorts caption,' 'photo caption,' 'video caption,' 'description for my pin,' or shares an image/video and asks for words to go with it. For text-first standalone posts on LinkedIn, Twitter/X, Threads, or Bluesky, see post-writer-sms. For multi-slide carousels, see carousel-writer-sms. For opening lines, see hook-writer-sms.
- `carousel-writer-sms` ✓ — When the user wants to write content for a LinkedIn carousel, Instagram carousel, Facebook carousel, TikTok photo carousel, Pinterest Idea Pin, or any swipeable multi-slide format. Also use when the user mentions 'carousel,' 'slides,' 'LinkedIn carousel,' 'Instagram carousel,' 'IG carousel,' 'photo carousel,' 'TikTok photo carousel,' 'Idea Pin,' 'Pinterest Idea Pin,' 'swipe post,' 'slide deck,' or 'visual content.' Outputs slide-by-slide text content (not visual design). For single posts, see post-writer-sms. For threads, see thread-writer-sms. For caption copy under each slide post, see caption-writer-sms.
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
- `comfyui` ✓ — Generate images, video, and audio via diffusion workflows.
- `ideation` ✓ — Generate project ideas via creative constraints.
- `design-md` ✓ — Author/validate/export Google's DESIGN.md token spec files.
- `excalidraw` ✓ — Hand-drawn Excalidraw JSON diagrams (arch, flow, seq).
- `humanizer` ✓ — Humanize text: strip AI-isms and add real voice.
- `manim-video` ✓ — Manim CE animations: 3Blue1Brown math/algo videos.
- `p5js` ✓ — p5.js sketches: gen art, shaders, interactive, 3D.
- `pixel-art` ✓ — Pixel art w/ era palettes (NES, Game Boy, PICO-8).
- `popular-web-designs` ✓ — 54 real design systems (Stripe, Linear, Vercel) as HTML/CSS.
- `pretext` ✓ — Build creative browser demos with DOM-free text layout.
- `sketch` ✓ — Throwaway HTML mockups: 2-3 design variants to compare.
- `songwriting-and-ai-music` ✓ — Songwriting craft and Suno AI music prompts.
- `touchdesigner-mcp` ✓ — Control TouchDesigner via twozero MCP.
- `cron-news-scraping` ✓ — Set up and maintain scheduled news-scraping cron jobs with firecrawl CLI. Covers the full pipeline: search, language filter, dedup, webhook POST, silent delivery.
- `cron-prompt-patterns` ✓ — Define and maintain standard prompt patterns for cron jobs in Hermes. Covers multi-action patterns, pronunciation rules, one-shot scheduling, and cron job formatting.
- `ai-memory` ✓ — Deploy, configure, and integrate the ai-memory MCP server — LLM providers, project management, wiki management, and Obsidian vault sync.
- `hermes-approvals-and-config-writes` ✓ — Use when editing Hermes config or commands get denied.
- `hermes-maintenance` ✓ — Safely update Hermes Agent, back up user data pre-update, and recover from update failures that wipe untracked user files (SOUL.md, USER.md, MEMORY.md, custom skills, state.db).
- `hermes-update-verification` ✓ — Verify a Hermes update applied; diagnose version confusion.
- `llm-api-cost-tracking` ✓ — Track, log, and visualize LLM API spending across providers. Covers local logging from API responses, platform CSV export, and proxy-based approaches. Provider-specific quirks in references/.
- `proportional-response` ✓ — Use when a request is simple or a tool fails. Reply short.
- `system-modification-protocol` ✓ — Protocol for setup, configuration, and system-modification tasks.
Governs how to approach, communicate, and execute changes to the VPS.
- `user-interaction-protocol` ✓ — Governs how the agent interacts with Giovani across ALL contexts.
Covers communication style, data sourcing, session startup, knowledge storage,
and correction handling. Broader than system-modification-protocol (system tasks only).
- `vps-service-deployment` ✓ — Deploy and expose services via Docker (Swarm) + EasyPanel + Traefik on the Hostinger KVM 2 VPS.
- `docker-host-interaction-troubleshooting` ✓ — Troubleshoot and resolve configuration issues related to Docker host interactions, container networking, volume mounts, and permission problems.
- `email-inbox-triage` ✓ — Triage an inbox: prioritize threads, draft replies safely.
- `himalaya` ✓ — Himalaya CLI: IMAP/SMTP email from terminal.
- `embedded-captions` ✓ — Add captions to a talking-head video. ONE catalog (CATALOG.md) of 36 visual identities behind two engines: column-flow (captions composited INTO the scene — matte occlusion + mix-blend; cream/ink/editorial/keynote/documentary/loud/neon/glitch/chrome/velocity) and themed constitutions (anchor/ordnance/terminal/neonsign/stardust/stomp/scoreboard/transit/vhs/arcade/dossier/laser/thunder/hologram/biolume/aurora/spectrum/papercut/popup/chalkboard/graffiti/brush/inkwater/ransom/lastpage/nightcity — e.g. a glyph-decode climax, a neon sign WRITTEN stroke by stroke, or the quiet `anchor` rail default). Route by identity, never by mode. Trigger on "captions/subtitles", "embed/cinematic captions", "VFX captions", "炸/特效/酷炫字幕", a named identity, or top-tier motion-graphics asks. Embedding every word is wrong for most talking-head content — `anchor` is the verbatim default. Runs locally end-to-end (transcribes and mattes the subject itself, no API key). Requires hyperframes and a single-subject clip (multi-shot clips ar...
- `faceless-explainer` ✓ — Turn arbitrary text — an article, notes, a topic, a brief — into a faceless explainer video: there is no site or footage to capture, so the visuals are invented per scene (typography, abstract graphics, diagrams, data-viz). Use for topic explainers, concept breakdowns, how-tos, listicles. Not a product promo (/product-launch-video) or a site tour (/website-to-video). Unclear → /hyperframes.
- `figma` ✓ — Import Figma content into a HyperFrames composition — rendered assets, brand tokens, components, storyboard sections → reconstructed motion (frames read as states, not slides) (REST/CLI), Figma Motion animations (MCP), and shaders (MCP source / native export). Use when the user pastes a figma.com link or asks to bring a Figma design, frame, logo, brand, or animation into a video/composition.
- `firecrawl` ✓ — Search, scrape, and interact with the web via the Firecrawl CLI. Use this skill whenever the user wants to search the web, find articles, research a topic, look something up online, scrape a webpage, grab content from a URL, get data from a website, crawl documentation, download a site, or interact with pages that need clicks or logins.

- `minecraft-modpack-server` ✓ — Host modded Minecraft servers (CurseForge, Modrinth).
- `pokemon-player` ✓ — Play Pokemon via headless emulator + RAM reads.
- `general-video` ✓ — The fallback workflow for authoring or editing any custom HyperFrames composition at any length or format — longer / multi-scene pieces, brand and sizzle reels, montages, title cards, static loops, freeform builds. Use only when no specialized workflow fits the input; routing table at /hyperframes.

- `codebase-inspection` ✓ — Inspect codebases w/ pygount: LOC, languages, ratios.
- `github-auth` ✓ — GitHub auth setup: HTTPS tokens, SSH keys, gh CLI login.
- `github-code-review` ✓ — Review PRs: diffs, inline comments via gh or REST.
- `github-issue-to-pr` ✓ — Carry a GitHub issue to a verified PR with honest CI state.
- `github-issues` ✓ — Create, triage, label, assign GitHub issues via gh or REST.
- `github-pr-workflow` ✓ — GitHub PR lifecycle: branch, commit, open, CI, merge.
- `github-repo-management` ✓ — Clone/create/fork repos; manage remotes, releases.
- `github-token-masking` ✓ — Como extrair tokens do .env sem o mascaramento do Hermes bloquear o valor — técnica de hex-encoding para uso em URLs de remote git.
- `hermes-gateway-tool-configuration` ✓ — Configure Hermes Gateway toolsets, plugins, Docker mounts, and credentials — covers the full path from 'tool not working in gateway' to 'tool working in Telegram/Discord'. Use when the user reports a tool that works in CLI but not in Hermes Gateway (Telegram/Discord), or needs to set up a new integration in the gateway.

- `hook-writer-sms` ✓ — When the user wants help writing opening lines, hooks, first sentences, video hooks, thumbnails titles, or pin titles that grab attention. Also use when the user mentions 'hook,' 'opening line,' 'first line,' 'scroll stopper,' 'attention grabber,' 'headline,' 'video hook,' 'on-screen hook,' 'YouTube title,' 'thumbnail text,' 'pin title,' 'how to start my post,' or 'nobody reads past my first line.' Covers text-first platforms (LinkedIn, Twitter/X, Threads, Bluesky) and visual-first platforms (Facebook, Instagram, TikTok, Pinterest, YouTube). Can be used standalone or invoked by other creation skills. For writing full posts, see post-writer-sms. For threads, see thread-writer-sms.
- `hyperframes-animation` ✓ — All animation knowledge for HyperFrames — atomic motion rules, multi-phase scene blueprints, scene transitions, broader motion-design techniques, AND the seven runtime adapters (GSAP default, plus Lottie, Three.js, Anime.js, CSS keyframes, Web Animations API, TypeGPU). Use for any motion or animation task: pick 2-4 rules and compose, or load a blueprint, or look up runtime-specific API (e.g. GSAP eases / Lottie player / Three.js mixer). Also covers auditing an existing composition's choreography (animation map) and 24 named text-animation effects. HyperFrames-native: single paused timeline, seek-safe, deterministic.
- `hyperframes-cli` ✓ — HyperFrames CLI dev loop. Use when running npx hyperframes init, add, catalog, capture, lint, check, snapshot, compare, grade-compare, preview, play, render, publish, cloud, feedback, lambda, doctor, browser, info, upgrade, skills, compositions, docs, benchmark, telemetry, transcribe, tts, or remove-background (validate/inspect/layout are deprecated aliases covered by check), or when troubleshooting the HyperFrames build/render environment. Entry point for HeyGen-hosted cloud rendering (`hyperframes cloud render / list / get / delete`) and self-managed AWS Lambda rendering (`hyperframes lambda deploy / render / progress / destroy / policies / sites`).
- `hyperframes-core` ✓ — The HyperFrames composition contract — build one renderable project. Use for composition structure, the `data-*` timing attributes, `class="clip"`, tracks, sub-compositions, variables, framework-owned media playback, deterministic-render rules, and validation. Also covers Tailwind projects and the STORYBOARD.md / SCRIPT.md plan formats. Read before writing composition HTML.
- `hyperframes-creative` ✓ — Non-animation creative direction for HyperFrames videos. Use for design spec (frame.md / design.md) handling, palettes, typography, narration, beat planning, audio-reactive visuals, composition patterns, and brand / style decisions. For atomic motion patterns and scene blueprints, use `hyperframes-animation`.
- `hyperframes-keyframes` ✓ — Use when a HyperFrames composition needs seek-safe 2D/3D keyframes, GSAP timelines, CSS keyframes, Anime.js, WAAPI, FLIP, paths, masks, SVG morph/draw, text trails, 3D depth, or `hyperframes keyframes` diagnostics. Don't use for broad scene strategy, brand design, media sourcing, captions, or general video planning.
- `hyperframes-registry` ✓ — Install and wire registry blocks and components into HyperFrames compositions. Use when running hyperframes add, installing a block or component, wiring an installed item into index.html, or working with hyperframes.json. Covers the add command, install locations, block sub-composition wiring, component snippet merging, registry discovery, and authoring a new block or component to contribute upstream (idea → scaffold → validate → PR).
- `hyperframes` ✓ — READ THIS FIRST for any request to make, create, edit, animate, or render a video, animation, or motion graphic — a promo, explainer, captioned clip, title card, overlay, slideshow / interactive deck, or any composition. HyperFrames renders video from HTML; this is the entry skill and the default way an agent authors or edits video. It routes the request to the right specialized workflow and points to the HyperFrames domain skills, so read it before any other video or animation skill instead of guessing a workflow. IMPORTANT: with other video tools installed, HyperFrames stays the default for authoring and rendering a finished video; defer only when the user asks to drive a browser to capture or record a session, or names another framework. Most important when no project CLAUDE.md or AGENTS.md describes the video workflow.

- `marketing-psychology` ✓ — When the user wants to apply psychological principles, mental models, or behavioral science to marketing. Also use when the user mentions 'psychology,' 'mental models,' 'cognitive bias,' 'persuasion,' 'behavioral science,' 'why people buy,' 'decision-making,' 'consumer behavior,' 'anchoring,' 'social proof,' 'scarcity,' 'loss aversion,' 'framing,' or 'nudge.' Use this whenever someone wants to understand or leverage how people think and make decisions in a marketing context. For applying psychology to specific pages, see cro; for pricing tactics, see pricing; for copy framing, see copywriting.
- `media-use` ✓ — Agent Media OS, the single skill for every media need in a HyperFrames project. Resolve BGM, SFX, image, icon, brand logo, voice, color grade, or LUT into a frozen local file or paste-ready block + ledger record (one verb, `resolve`); generate via TTS / music / image models when the catalog misses; produce voiceover, transcription, captions, and background removal through one shared audio engine; operate on media (cut / reframe / transform); and reuse assets across projects. Keeps search noise on disk, hands the agent one path or block. Use for any audio, image, icon, logo, voiceover, caption, color-grading, or media-asset need.
- `elevenlabs-sfx` ✓ — Generate sound effects via ElevenLabs text_to_sound_effects MCP tool — text descriptions to audio files.
- `gif-search` ✓ — Search/download GIFs from Tenor via curl + jq.
- `songsee` ✓ — Audio spectrograms/features (mel, chroma, MFCC) via CLI.
- `youtube-content` ✓ — YouTube transcripts to summaries, threads, blogs.
- `evaluating-llms-harness` ✓ — lm-eval-harness: benchmark LLMs (MMLU, GSM8K, etc.).
- `weights-and-biases` ✓ — W&B: log ML experiments, sweeps, model registry, dashboards.
- `huggingface-hub` ✓ — HuggingFace hf CLI: search/download/upload models, datasets.
- `llama-cpp` ✓ — llama.cpp local GGUF inference + HF Hub model discovery.
- `serving-llms-vllm` ✓ — vLLM: high-throughput LLM serving, OpenAI API, quantization.
- `dspy` ✓ — DSPy: declarative LM programs, auto-optimize prompts, RAG.
- `motion-graphics` ✓ — A short, design-led motion graphic where motion is the message — kinetic typography, stat count-up, chart/data-viz hit, logo sting / brand lockup, lower-third / callout / social overlay, animated map (highlight regions, connect places, zoom to a location), animated tweet / news-article / headline, webpage / UI animation (scroll, cursor, callouts), or fusing a real image's geometry into a chart. Usually under 10s (up to ~30s), no narration or live-action subject; renders to MP4 or transparent overlay. Longer / narrated / multi-scene → /general-video. Unclear → /hyperframes.

- `music-to-video` ✓ — Turn a music track (an audio file, a video to pull audio from, or a track generated from a mood brief) into a beat-synced video — lyric video, slideshow, or kinetic promo. The music drives all pacing; any user-supplied images/videos are cut onto the same beat grid, and a complete video needs zero assets. Narrated pieces → the input-matched workflow (see /hyperframes). Unclear → /hyperframes.
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
- `open-midia` ✓ — Use ao refinar copy dos projetos Open Mídia.
- `pr-to-video` ✓ — Turn a GitHub pull request (a PR URL, owner/repo#N, or 'this PR' in a checked-out repo) into a code-change explainer video — changelog, feature reveal, fix, or refactor walkthrough built from the diff, commits, and files: the input is a code change, not a website. Not a product promo (/product-launch-video) or a no-PR topic explainer (/faceless-explainer). Unclear → /hyperframes.
- `product-launch-video` ✓ — Turn a product or marketing URL, pasted script, or brief into a product launch / promo video — SaaS promos, feature reveals, product demos, app and company launches. Use when the user wants to market, launch, promote, or reveal a product; the default for any commercial URL. Not a general site tour (/website-to-video). Unclear → /hyperframes.
- `airtable` ✓ — Airtable REST API via curl. Records CRUD, filters, upserts.
- `box` ✓ — Box manages cloud files, sharing, search, and metadata.
- `document-to-action-items` ✓ — Extract cited obligations, deadlines, tasks from documents.
- `docx` ✓ — Create, read, edit, template, and review Word .docx files.
- `google-workspace` ✓ — Gmail, Calendar, Drive, Docs, Sheets via gws CLI or Python.
- `maps` ✓ — Geocode, POIs, routes, timezones via OpenStreetMap/OSRM.
- `meeting-action-items` ✓ — Turn meeting notes into cited decisions, owners, tickets.
- `nano-pdf` ✓ — Edit text in existing PDFs via natural-language prompts.
- `notion` ✓ — Notion API + ntn CLI: pages, databases, markdown, Workers.
- `ocr-and-documents` ✓ — Extract text from PDFs/scans (pymupdf, marker-pdf).
- `pdf` ✓ — Create, read, merge, fill, and secure PDF files.
- `powerpoint` ✓ — Create, read, edit .pptx decks with python-pptx.
- `product-price-monitor` ✓ — Watch product, flight, or listing prices; alert on target.
- `session-librarian` ✓ — Organize sessions by prompt: find, rename, archive, prune.
- `teams-meeting-pipeline` ✓ — Teams meeting summaries, job replay, Graph subscriptions.
- `weekly-review-planning` ✓ — Weekly reset: commitments, stalled work, next-week plan.
- `xlsx` ✓ — Create, read, edit Excel .xlsx workbooks and CSVs.
- `agent-memory-architecture` ✓ — Design agent memory systems combining manual knowledge bases (Obsidian vaults) with automatic capture tools (ai-memory). Covers vault structure, complementary layers, and integration patterns for cross-agent persistence.
- `arxiv` ✓ — Search arXiv papers by keyword, author, category, or ID.
- `blocked-page-recovery` ✓ — Recover blocked/paywalled/WAF'd pages via fallbacks.
- `blogwatcher` ✓ — Monitor blogs and RSS/Atom feeds via blogwatcher-cli tool.
- `competitor-news-monitor` ✓ — Watch named companies for material news; cited digests.
- `grounded-citations` ✓ — Ground answers and documents in cited, verifiable sources.
- `llm-wiki-ai-memory` ✓ — Complemento ao llm-wiki para uso com ai-memory como backend — UUID folders, OKF principles, renomear arquivos em wikis git-backed, e deduplicação de conceitos.
- `llm-wiki` ✓ — Karpathy's LLM Wiki: build/query interlinked markdown KB.
- `research-paper-writing` ✓ — Write ML papers for NeurIPS/ICML/ICLR: design→submit.
- `web-research-delivery` ✓ — Answer research questions; web fallbacks when tools fail.
- `web-research-fallbacks` ✓ — Use when web_search fails. Direct-API web fetch paths.
- `senior-react-video-developer` ✓ — Skill para desenvolvimento de videos com React e Remotion. Instalacao, componentes, manipulacao de midia, renderizacao e exportacao.
- `seo-audit` ✓ — When the user wants to audit, review, or diagnose SEO issues on their site. Also use when the user mentions "SEO audit," "technical SEO," "why am I not ranking," "SEO issues," "on-page SEO," "meta tags review," "SEO health check," "my traffic dropped," "lost rankings," "not showing up in Google," "site isn't ranking," "Google update hit me," "page speed," "core web vitals," "crawl errors," or "indexing issues." Use this even if the user just says something vague like "my SEO is bad" or "help with SEO" — start with an audit. For building pages at scale to target keywords, see programmatic-seo. For adding structured data, see schema. For AI search optimization, see ai-seo.
- `skill-catalog-format` ✓ — Template de formatação para skills no catálogo do Telegram no tópico Skills
- `slideshow` ✓ — Author a HyperFrames slideshow — a presentation, pitch deck, or interactive deck with discrete slides, fragment reveals, branching, hotspot navigation, and built-in presenter mode with speaker notes; also converts an existing page into a deck. Output is a navigable deck, not a rendered MP4. If the user didn't explicitly ask for a slideshow, confirm before authoring. Unclear → /hyperframes.
- `openhue` ✓ — Control Philips Hue lights, scenes, rooms via OpenHue CLI.
- `social-content` ✓ — When the user wants help creating, scheduling, or optimizing social media content for LinkedIn, Twitter/X, Instagram, TikTok, Facebook, or other platforms. Also use when the user mentions 'LinkedIn post,' 'Twitter thread,' 'social media,' 'content calendar,' 'social scheduling,' 'engagement,' or 'viral content.' This skill covers content creation, repurposing, and platform-specific strategies.
- `xurl` ✓ — X/Twitter via xurl CLI: raw post search, posting, DM, media.
- `social` ✓ — When the user wants help creating, scheduling, or optimizing social media content for LinkedIn, Twitter/X, Instagram, TikTok, Facebook, or other platforms, or wants to do social listening and engagement triage. Also use when the user mentions 'LinkedIn post,' 'Twitter thread,' 'social media,' 'content calendar,' 'social scheduling,' 'engagement,' 'viral content,' 'what should I post,' 'repurpose this content,' 'tweet ideas,' 'LinkedIn carousel,' 'social media strategy,' 'grow my following,' 'TikTok video,' 'Reels,' 'Shorts,' 'video script,' 'video hook,' 'short-form video,' 'create a reel,' 'social listening,' 'brand mentions,' 'competitor monitoring,' 'top posts to comment on,' or 'find people asking for.' Use this for social media content creation, repurposing, scheduling, short-form video scripting, and social listening. For broader content strategy, see content-strategy. For paid ads, see ad-creative. For earned media, see public-relations.
- `ai-memory-wiki` ✓ — Gerenciar vault wiki markdown com OKF + ai-memory — editar, commit, push, sincronizar SQLite
- `dogfood` ✓ — Exploratory QA of web apps: find bugs, evidence, reports.
- `hermes-agent-skill-authoring` ✓ — Author in-repo SKILL.md files: frontmatter and structure.
- `inspecting-hermes-desktop-dom` ✓ — Read the live Hermes desktop DOM/CSS over CDP.
- `node-inspect-debugger` ✓ — Debug Node.js via --inspect + Chrome DevTools Protocol CLI.
- `plan` ✓ — Write a markdown plan to .hermes/plans/; no execution.
- `python-debugpy` ✓ — Debug Python: pdb REPL + debugpy remote (DAP).
- `requesting-code-review` ✓ — Pre-commit review: security scan, quality gates, auto-fix.
- `simplify-code` ✓ — Parallel 4-agent cleanup of recent code changes.
- `spike` ✓ — Throwaway experiments to validate an idea before build.
- `systematic-debugging` ✓ — 4-phase root cause debugging: understand bugs before fixing.
- `test-driven-development` ✓ — TDD: enforce RED-GREEN-REFACTOR, tests before code.
- `spotify-helper` ✓ — Skill para ajudar a controlar e gerenciar funcionalidades do Spotify via Hermes Agent, garantindo uma experiencia proativa e intuitiva.
- `talking-head-recut` ✓ — Package an existing talking-head / interview / podcast video with timed, designed GRAPHIC OVERLAY cards — kinetic titles, lower-thirds, data callouts, quotes, side panels, picture-in-picture — synced to the transcript, on a 16:9 / 9:16 / 4:5 canvas of your choice; the clip plays untouched underneath. Trigger on "graphic overlays", "on-screen graphics", "package / dress up my video". Not plain subtitles (/embedded-captions). Unclear → /hyperframes.
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
- `telegram-rich-delivery-maintenance` ✓ — Use when Telegram replies arrive flat; restore rich.
- `mcp-server-trello` ✓ — Trello MCP Server skill for board discovery, card workflows, checklist management, comments, attachments, labels, members, board/workspace selection, and health monitoring through the bundled @delorenj/mcp-server-trello server.
- `website-to-video` ✓ — Capture a general website/URL and turn it into a video OF the site — tour, showcase, or social clip built from captured screenshots and the site's own brand assets. Use for portfolio / blog / docs / landing-page showcases. Not a product launch or promo, even from a URL (/product-launch-video). Unclear → /hyperframes.

## Webhooks

- `?` ✗

## Toolsets

- `web` ✓ — web_search, web_extract
- `browser` ✓ — navigate, click, type, scroll
- `terminal` ✓ — terminal, process
- `file` ✓ — read, write, patch, search
- `code_execution` ✓ — execute_code
- `vision` ✓ — vision_analyze
- `video` ✗ — video_analyze (requires video-capable model)
- `image_gen` ✓ — image_generate
- `video_gen` ✗ — video_generate (text/image/reference)
- `bfl` ✓ — bfl_flux3_*
- `x_search` ✗ — x_search (requires xAI OAuth or XAI_API_KEY)
- `tts` ✓ — text_to_speech
- `stt` ✓ — voice transcription (gateway voice messages + voice mode)
- `skills` ✓ — list, view, manage
- `todo` ✓ — todo
- `memory` ✓ — persistent memory across sessions
- `context_engine` ✗ — runtime tools from the active context engine
- `session_search` ✓ — search past conversations
- `clarify` ✓ — clarify
- `delegation` ✓ — delegate_task
- `cronjob` ✓ — create/list/update/pause/resume/run, with optional attached skills
- `homeassistant` ✗ — smart home device control
- `spotify` ✗ — playback, search, playlists, library
- `discord` ✗ — fetch messages, search members, create thread
- `discord_admin` ✗ — list channels/roles, pin, assign roles
- `yuanbao` ✗ — group info, member queries, DM
- `computer_use` ✓ — background desktop control via cua-driver
- `a2a` ✗ — A2A (Agent-to-Agent) protocol v1.0 support for Hermes Agent — both directions of the open Linux Foundation standard for inter-agent communication.
OUTBOUND (client tools): a2a_discover, a2a_call, a2a_list, a2a_history, and a2a_orchestrate let the agent fetch another agent's Agent Card and send it tasks over JSON-RPC — works with any A2A-compliant peer (Hermes, LangChain, CrewAI, Google ADK, OpenClaw, ...).
INBOUND (platform adapter): exposes Hermes as an A2A-discoverable agent. An Agent Card is served at /.well-known/agent-card.json (v1.0 canonical path; legacy agent.json also answers) and incoming tasks are routed into the agent's live gateway session like any other platform — so the agent that replies is the same one talking to its user, with full memory and context, not a throwaway clone.
Security is on by default: no bearer token configured => localhost-only bind. Inbound task text passes through prompt-injection filters; outbound text is scrubbed of credential-shaped strings; every exchange is audit-logged and persisted to disk outside the context-compaction pipeline so conversations survive compaction and restarts.
Pure stdlib transport (http.server + urllib) — no a2a-sdk dependency required.

## Perfis

- `default` — deepseek-v4-flash
- `gio` — deepseek-v4-flash
- `gio2` — deepseek-v4-flash

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
- `A2A` ✗ — No extra packages needed (stdlib only)
- `Buzz` ✗ — Requires the buzz CLI binary (https://github.com/block/buzz) on PATH or at BUZZ_CLI_PATH
- `iMessage via Photon` ✗ — Use Hermes through iMessage via Photon's managed Spectrum platform.
- `IRC` ✗ — Relay messages between an IRC channel (or DMs) and Hermes.
- `LINE` ✗ — Use Hermes from LINE via the LINE Messaging API webhook.
- `Microsoft Graph Webhook` ✗ — Receive Microsoft Graph change notifications (Teams meetings, Outlook, …).
- `Microsoft Teams` ✗ — Connect Hermes to Microsoft Teams chats via the Bot Framework.
- `ntfy` ✗ — Chat with Hermes over ntfy push topics (ntfy.sh or self-hosted).
- `Raft` ✗ — Join a Raft workspace as an external agent.
- `Relay (experimental)` ✗ — Generic relay adapter fronted by the Hermes Relay connector.
- `SimpleX Chat` ✗ — Talk to Hermes over SimpleX Chat via a local simplex-chat daemon.
- `WhatsApp Cloud API` ✗ — Use Hermes via Meta's hosted WhatsApp Cloud API (no local bridge).

---

## Conexões

- [[wiki/systems/hermes-endpoints.md]]
- [[wiki/systems/hermes.md]]
- [[wiki/systems/vps.md]]
