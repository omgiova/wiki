---
type: system
tags: [hermes, api, rest, openapi]
title: Hermes Agent — API REST (OpenAPI)
description: Referência completa dos endpoints REST do Hermes Agent, gerada automaticamente do /openapi.json — seção Interface do sistema Hermes
timestamp: 2026-09-14T03:00:01-03:00
status: stable
---

# Hermes Agent — API REST

> **Gerado automaticamente** a partir de `GET /openapi.json` (Hermes Agent v0.21.1, OAS 3.1).
> Para atualizar manualmente: execute `/root/scripts/update-hermes-wiki.sh`
> Última atualização: 2026-09-14T03:00:01-03:00 | Total de endpoints: 273

- **Base URL:** `http://localhost:9119`
- **Auth:** POST `/auth/password-login` com `{"username":"...","password":"...","provider":"basic"}`
- **Docs interativas:** `http://localhost:9119/docs`

---

## Endpoints por grupo

### actions
- `GET /api/actions/{name}/status` **Get Action Status** — Tail an action log and report whether the process is still running.

### analytics
- `GET /api/analytics/models` **Get Models Analytics** — Return model analytics without blocking the serving event loop.
- `GET /api/analytics/usage` **Get Usage Analytics** — ``days`` is clamped to 1-365 (idea from #74778): huge or non-positive

### assets
- `GET /assets/{filename}.css` **Serve Css**

### audio
- `GET /api/audio/elevenlabs/voices` **Get Elevenlabs Voices** — Return ElevenLabs voices when an API key is configured.
- `POST /api/audio/speak` **Speak Text** — Synthesize speech and return audio as base64 data URL.
- `POST /api/audio/transcribe` **Transcribe Audio Upload**
- `POST /api/audio/tts-lease` **Tts Lease** — Desktop TTS-output toggles as warm-up / release signals.
- `GET /api/audio/voice-config` **Get Client Voice Config** — The active profile's STT/TTS config for CLIENT-DIRECT voice.

### auth
- `GET /api/auth/me` **Auth Me** — Return the verified session as JSON. Auth-required (gate enforces).
- `GET /api/auth/providers` **Auth Providers**
- `POST /api/auth/ws-ticket` **Auth Ws Ticket** — Mint a 30s single-use ticket for a WS upgrade (browsers cannot set
- `GET /auth/callback` **Auth Callback**
- `GET /auth/login` **Auth Login**
- `POST /auth/logout` **Auth Logout**
- `GET /auth/native/authorize` **Auth Native Authorize** — Begin an RFC 8252 native-app login: stash a pending broker authorization keyed by an
- `POST /auth/native/refresh` **Auth Native Refresh** — Rotate a desktop-held refresh token (mirrors the gate's ``_attempt_refresh``): every
- `POST /auth/native/token` **Auth Native Token** — Exchange a loopback gateway code + PKCE verifier for bearer tokens. The code is consumed
- `POST /auth/password-login` **Auth Password Login** — Authenticate a username/password against a password provider.

### chat
- `POST /api/chat/image-upload` **Upload Chat Image** — Persist a browser clipboard image where the embedded TUI can read it.

### config
- `GET /api/config` **Get Config**
- `PUT /api/config` **Update Config**
- `GET /api/config/defaults` **Get Defaults**
- `GET /api/config/raw` **Get Config Raw** — Raw config.yaml text plus its resolved path.
- `PUT /api/config/raw` **Update Config Raw**
- `GET /api/config/schema` **Get Schema**

### credentials
- `GET /api/credentials/pool` **List Credential Pool**
- `POST /api/credentials/pool` **Add Credential Pool Entry**
- `DELETE /api/credentials/pool/{provider}/{index}` **Remove Credential Pool Entry** — Remove a pool entry (``index`` is 1-based, as listed).

### cron
- `GET /api/cron/blueprints` **List Cron Blueprints** — Blueprint catalog as form schemas; the ``deliver`` slot's options are
- `POST /api/cron/blueprints/instantiate` **Instantiate Blueprint** — Fill a blueprint's slots and create the cron job (form-submit path).
- `GET /api/cron/delivery-targets` **Get Cron Delivery Targets** — Delivery targets for the cron dropdown: implicit ``local`` plus the
- `POST /api/cron/fire` **Cron Fire Webhook** — Chronos managed-cron fire webhook (NAS -> agent) — gateway forwarder.
- `GET /api/cron/jobs` **List Cron Jobs**
- `POST /api/cron/jobs` **Create Cron Job**
- `GET /api/cron/jobs/{job_id}` **Get Cron Job**
- `PUT /api/cron/jobs/{job_id}` **Update Cron Job**
- `DELETE /api/cron/jobs/{job_id}` **Delete Cron Job**
- `POST /api/cron/jobs/{job_id}/pause` **Pause Cron Job**
- `POST /api/cron/jobs/{job_id}/resume` **Resume Cron Job**
- `GET /api/cron/jobs/{job_id}/runs` **List Cron Job Runs**
- `POST /api/cron/jobs/{job_id}/trigger` **Trigger Cron Job**

### curator
- `GET /api/curator` **Get Curator Status**
- `PUT /api/curator/paused` **Set Curator Paused**
- `POST /api/curator/run` **Run Curator** — Trigger a curator review now (backgrounded; tail via action status).

### dashboard
- `POST /api/dashboard/agent-plugins/install` **Post Agent Plugin Install**
- `DELETE /api/dashboard/agent-plugins/{name}` **Delete Agent Plugin**
- `POST /api/dashboard/agent-plugins/{name}/disable` **Post Agent Plugin Disable**
- `POST /api/dashboard/agent-plugins/{name}/enable` **Post Agent Plugin Enable**
- `POST /api/dashboard/agent-plugins/{name}/update` **Post Agent Plugin Update**
- `GET /api/dashboard/font` **Get Dashboard Font** — Return the active font override (``"theme"`` = use the theme's font).
- `PUT /api/dashboard/font` **Set Dashboard Font** — Set the font override (config.yaml). Unknown ids coerce to ``"theme"`` rather than
- `PUT /api/dashboard/plugin-providers` **Put Plugin Providers** — Persist memory provider / context engine selection (writes config.yaml).
- `GET /api/dashboard/plugins` **Get Dashboard Plugins** — Return discovered dashboard plugins (excludes user-hidden and non-enabled ones).
- `GET /api/dashboard/plugins/catalog` **Get Plugins Catalog** — Curated plugin catalog merged with installed state (session protected).
- `GET /api/dashboard/plugins/hub` **Get Plugins Hub** — Unified agent plugins + dashboard extension metadata (session protected).
- `GET /api/dashboard/plugins/rescan` **Rescan Dashboard Plugins** — Force re-scan of dashboard plugins.
- `POST /api/dashboard/plugins/{name}/visibility` **Post Plugin Visibility** — Toggle a plugin's sidebar visibility (persists to config.yaml dashboard.hidden_plugins).
- `PUT /api/dashboard/theme` **Set Dashboard Theme** — Set the active dashboard theme (persists to config.yaml).
- `GET /api/dashboard/themes` **Get Dashboard Themes** — Available themes + the active one. Built-ins ship name/label/description only

### dashboard-plugins
- `GET /dashboard-plugins/{plugin_name}/{file_path}` **Serve Plugin Asset** — Serve static assets from a dashboard plugin's ``dashboard/`` directory.

### egress
- `GET /api/egress/status` **Get Egress Status** — Dashboard/Desktop-readable egress proxy status and remediation text.

### env
- `GET /api/env` **Get Env Vars**
- `PUT /api/env` **Set Env Var**
- `DELETE /api/env` **Remove Env Var**
- `POST /api/env/reveal` **Reveal Env Var** — Return the real (unredacted) value of a single env var.

### files
- `GET /api/files` **List Managed Files**
- `DELETE /api/files` **Delete Managed File**
- `GET /api/files/download` **Download Managed File** — Stream a managed file as an attachment download.
- `POST /api/files/mkdir` **Create Managed Directory**
- `GET /api/files/read` **Read Managed File**
- `HEAD /api/files/stream` **Stream Managed File** — Stream managed audio/video inline with HTTP Range support — Electron's
- `GET /api/files/stream` **Stream Managed File** — Stream managed audio/video inline with HTTP Range support — Electron's
- `POST /api/files/upload` **Upload Managed File**
- `POST /api/files/upload-stream` **Upload Managed File Stream** — Chunked multipart upload: constant memory and no base64 inflation, unlike

### fs
- `GET /api/fs/default-cwd` **Fs Default Cwd**
- `GET /api/fs/download` **Fs Download**
- `GET /api/fs/git-root` **Fs Git Root**
- `GET /api/fs/list` **Fs List**
- `GET /api/fs/read-data-url` **Fs Read Data Url**
- `GET /api/fs/read-text` **Fs Read Text**
- `POST /api/fs/write-text` **Fs Write Text** — Overwrite (or create) a UTF-8 text file for the in-app spot editor.

### gateway
- `POST /api/gateway/drain` **Gateway Drain** — Begin or cancel an external (NAS-driven) gateway drain.
- `POST /api/gateway/restart` **Restart Gateway** — Kick off a ``hermes gateway restart`` in the background.
- `POST /api/gateway/start` **Start Gateway**
- `POST /api/gateway/stop` **Stop Gateway**

### git
- `GET /api/git/base-branches` **Git Base Branches Route**
- `POST /api/git/branch/switch` **Git Branch Switch Route**
- `GET /api/git/branches` **Git Branches Route**
- `GET /api/git/file-diff` **Git File Diff Route**
- `GET /api/git/gh-auth` **Gh Auth Status Route** — ``{"available", "authenticated"}`` for the `gh` CLI; cached 5 min
- `POST /api/git/review/commit` **Git Commit Route**
- `GET /api/git/review/commit-context` **Git Commit Context Route**
- `POST /api/git/review/create-pr` **Git Create Pr Route**
- `GET /api/git/review/diff` **Git Review Diff Route**
- `GET /api/git/review/list` **Git Review List Route**
- `POST /api/git/review/pr-list` **Git Pr List Route**
- `POST /api/git/review/push` **Git Push Route**
- `GET /api/git/review/rev-parse` **Git Rev Parse Route**
- `POST /api/git/review/revert` **Git Revert Route**
- `GET /api/git/review/ship-info` **Git Ship Info Route**
- `POST /api/git/review/stage` **Git Stage Route**
- `POST /api/git/review/unstage` **Git Unstage Route**
- `GET /api/git/status` **Git Status Route**
- `POST /api/git/worktree/add` **Git Worktree Add Route**
- `POST /api/git/worktree/remove` **Git Worktree Remove Route**
- `GET /api/git/worktrees` **Git Worktrees Route**

### health
- `GET /api/health` **Get Health** — Lightweight process liveness for desktop/backend readiness probes.

### hermes
- `POST /api/hermes/update` **Update Hermes** — Kick off ``hermes update`` in the background.
- `GET /api/hermes/update/check` **Check Hermes Update** — Report whether a Hermes update is available, without applying it.
- `GET /api/hermes/update/receipt` **Get Update Receipt** — The FULL latest update receipt (steps, skips, gateway restart outcome, fleet

### learning
- `GET /api/learning/graph` **Get Learning Graph** — Learning graph for the desktop panel: profile-scoped learned skills + memory chunks.
- `GET /api/learning/node` **Get Learning Node** — Current content of a journey node (skill SKILL.md or memory chunk), for an edit prefill.
- `DELETE /api/learning/node` **Delete Learning Node** — Delete a journey node — skills are archived (restorable), memories removed.
- `PUT /api/learning/node` **Update Learning Node** — Rewrite a journey node's content (SKILL.md or memory chunk).

### local-models
- `POST /api/local-models/activate` **Local Models Activate** — Make a downloaded model the default for new chats: a config write via the same machinery as
- `GET /api/local-models/catalog` **Local Models Catalog** — Every entry answers up front: how big is the download, will it fit, what context/speed shape will I
- `POST /api/local-models/download` **Local Models Download** — Accepts either a family id (downloads this machine's selected variant) or an exact variant model_id.
- `POST /api/local-models/download-browsed` **Local Models Download Browsed** — Download an arbitrary HF GGUF into the managed models dir. Once landed it is a normal staged model (the
- `POST /api/local-models/eject` **Local Models Eject** — Free a loaded model's GPU memory now; only demand (the next message) reloads it — residency v2 has no
- `GET /api/local-models/hardware` **Local Models Hardware** — The budget as plain facts, polled by the pane and statusbar. Sync def: shells out to nvidia-smi — threadpool.
- `GET /api/local-models/jobs` **Local Models Jobs** — All recent jobs, running first — the pane and app-level poller rediscover in-flight work here after a remount.
- `GET /api/local-models/jobs/{job_id}` **Local Models Job**
- `DELETE /api/local-models/models/{model_id}` **Local Models Delete** — Remove every split part plus private assets, then bounce the router off the request thread (deleting
- `POST /api/local-models/quickstart` **Local Models Quickstart** — One job: install the runtime (if missing), download this machine's build of the recommended model (if
- `POST /api/local-models/runtime/install` **Local Models Runtime Install**
- `GET /api/local-models/search` **Local Models Search** — Full-text HF search over GGUF models — the firehose behind the curated catalog; fit pills come from /search/files.
- `GET /api/local-models/search/files` **Local Models Search Files** — Servable GGUFs in one HF repo with a rough pre-download fit verdict per quant (file size + conservative
- `POST /api/local-models/server` **Local Models Server** — Turn the local engine off (stop the server, free ALL GPU memory, disable auto-start) or back on. Unlike
- `POST /api/local-models/sideload` **Local Models Sideload** — Register a GGUF already on this machine: link it into the managed models dir (copy only when linking is
- `GET /api/local-models/status` **Local Models Status** — Cheap, immediate: config state + installed runtime + staged models + supervisor state (GPU facts live

### login
- `GET /login` **Login Page**

### logs
- `GET /api/logs` **Get Logs**

### mcp
- `GET /api/mcp/catalog` **List Mcp Catalog** — Browse the Nous-approved MCP catalog (optional-mcps/ manifests), each
- `POST /api/mcp/catalog/install` **Install Mcp Catalog Entry** — Install a catalog MCP into config.yaml (declared env vars go to .env
- `GET /api/mcp/oauth/callback/{server_name}` **Mcp Oauth Callback**
- `GET /api/mcp/oauth/flows/{flow_id}` **Mcp Oauth Flow Status**
- `DELETE /api/mcp/oauth/flows/{flow_id}` **Cancel Mcp Oauth Flow** — Cancel an in-flight flow. mark_error unblocks the worker so it frees the
- `GET /api/mcp/servers` **List Mcp Servers**
- `POST /api/mcp/servers` **Add Mcp Server**
- `PUT /api/mcp/servers` **Replace Mcp Servers** — Replace the entire ``mcp_servers`` map (the mcp.json editor's save) —
- `DELETE /api/mcp/servers/{name}` **Remove Mcp Server**
- `POST /api/mcp/servers/{name}/auth` **Auth Mcp Server** — Start MCP OAuth and hand the authorization URL to the dashboard browser.
- `PUT /api/mcp/servers/{name}/enabled` **Set Mcp Server Enabled** — Toggle ``enabled`` (takes effect on next session/gateway); disabled
- `POST /api/mcp/servers/{name}/test` **Test Mcp Server** — Connect to the server, list its tools, disconnect.

### media
- `GET /api/media` **Get Media** — Return a gateway-local image as a base64 data URL for remote clients

### memory
- `GET /api/memory` **Get Memory Status**
- `PUT /api/memory/provider` **Set Memory Provider**
- `GET /api/memory/providers/{name}/config` **Get Memory Provider Config**
- `PUT /api/memory/providers/{name}/config` **Update Memory Provider Config**
- `POST /api/memory/providers/{name}/setup` **Setup Memory Provider**
- `POST /api/memory/providers/{provider}/oauth/start` **Start Memory Oauth** — Begin a provider's zero-CLI OAuth flow (browser + loopback listener); returns immediately, poll status.
- `GET /api/memory/providers/{provider}/oauth/status` **Memory Oauth Status** — Poll a provider's OAuth flow: idle | pending | connected | error.
- `POST /api/memory/reset` **Reset Memory**

### messaging
- `GET /api/messaging/platforms` **Get Messaging Platforms**
- `PUT /api/messaging/platforms/{platform_id}` **Update Messaging Platform**
- `POST /api/messaging/platforms/{platform_id}/test` **Test Messaging Platform**
- `POST /api/messaging/telegram/onboarding/start` **Start Telegram Onboarding**
- `GET /api/messaging/telegram/onboarding/{pairing_id}` **Get Telegram Onboarding Status**
- `DELETE /api/messaging/telegram/onboarding/{pairing_id}` **Cancel Telegram Onboarding**
- `POST /api/messaging/telegram/onboarding/{pairing_id}/apply` **Apply Telegram Onboarding**
- `POST /api/messaging/whatsapp/onboarding/start` **Start Whatsapp Onboarding**
- `GET /api/messaging/whatsapp/onboarding/{pairing_id}` **Get Whatsapp Onboarding Status**
- `DELETE /api/messaging/whatsapp/onboarding/{pairing_id}` **Cancel Whatsapp Onboarding**
- `POST /api/messaging/whatsapp/onboarding/{pairing_id}/apply` **Apply Whatsapp Onboarding**

### model
- `GET /api/model/auxiliary` **Get Auxiliary Models** — Current auxiliary task assignments: ``{"tasks": [{task, provider, model,
- `GET /api/model/info` **Get Model Info** — Resolved metadata for the configured model: auto-detected vs configured
- `GET /api/model/moa` **Get Moa Models** — Return the configured Mixture-of-Agents provider/model slots.
- `PUT /api/model/moa` **Set Moa Models** — Persist the Mixture-of-Agents provider/model slots.
- `GET /api/model/options` **Get Model Options** — Authenticated providers + curated model lists — REST twin of the ``model.options``
- `GET /api/model/recommended-default` **Get Recommended Default Model** — Recommended default model for a freshly-authenticated provider, mirroring
- `POST /api/model/set` **Set Model Assignment** — Assign a model to the main slot or an auxiliary task slot. Writes

### ops
- `POST /api/ops/backup` **Run Backup**
- `GET /api/ops/backup/download` **Download Dashboard Backup**
- `GET /api/ops/checkpoints` **List Checkpoints** — /rollback shadow-store checkpoints (read-only): count + size per session
- `POST /api/ops/checkpoints/prune` **Prune Checkpoints**
- `POST /api/ops/config-migrate` **Run Config Migrate**
- `POST /api/ops/debug-share` **Run Debug Share Endpoint** — Upload a redacted debug report + full logs and return the paste URLs. Synchronous,
- `POST /api/ops/doctor` **Run Doctor**
- `POST /api/ops/dump` **Run Dump**
- `GET /api/ops/hooks` **List Hooks** — Configured shell hooks with consent (allowlist) status, whether the
- `POST /api/ops/hooks` **Create Hook** — Add a shell hook to config.yaml and optionally record consent.
- `DELETE /api/ops/hooks` **Delete Hook** — Remove a hook from config.yaml and revoke its consent allowlist entry.
- `POST /api/ops/import` **Run Import**
- `POST /api/ops/import-upload` **Run Import Upload**
- `POST /api/ops/prompt-size` **Run Prompt Size**
- `POST /api/ops/security-audit` **Run Security Audit**

### pairing
- `GET /api/pairing` **List Pairing**
- `POST /api/pairing/approve` **Approve Pairing**
- `POST /api/pairing/clear-pending` **Clear Pending Pairing**
- `POST /api/pairing/revoke` **Revoke Pairing**

### portal
- `GET /api/portal` **Get Portal Status**

### profiles
- `GET /api/profiles` **List Profiles Endpoint**
- `POST /api/profiles` **Create Profile Endpoint**
- `GET /api/profiles/active` **Get Active Profile Endpoint** — ``active`` is the sticky default written by ``hermes profile use`` (what new CLI
- `POST /api/profiles/active` **Set Active Profile Endpoint** — Set the sticky active profile (mirrors ``hermes profile use``); does not retarget the
- `POST /api/profiles/import` **Import Profile Endpoint**
- `GET /api/profiles/projects/tree` **Get Profiles Projects Tree** — Project tree for every profile at once, for the all-profiles sidebar.
- `GET /api/profiles/sessions` **Get Profiles Sessions** — Unified, read-only session list aggregated across ALL profiles: opens each profile's
- `POST /api/profiles/sessions/pull-requests` **Post Profiles Sessions Pull Requests** — The PR each of these sessions opened, recovered from its own transcript: a session
- `GET /api/profiles/sessions/sidebar` **Get Profiles Sessions Sidebar** — Batched sidebar session slices (recents / cron / messaging) — one profile-DB open per
- `PATCH /api/profiles/{name}` **Rename Profile Endpoint**
- `DELETE /api/profiles/{name}` **Delete Profile Endpoint** — The dashboard collects the user's confirmation in its own dialog, so ``yes=True``
- `POST /api/profiles/{name}/describe-auto` **Describe Profile Auto Endpoint** — Auto-generate a profile's description via the auxiliary LLM (mirrors ``hermes profile
- `PUT /api/profiles/{name}/description` **Update Profile Description Endpoint** — Set or clear a profile's role description (kanban routing signal), stored as
- `GET /api/profiles/{name}/desktop-overlay` **Get Profile Desktop Overlay** — The desktop appearance/interface overlay bundled with an imported profile
- `POST /api/profiles/{name}/export` **Export Profile Endpoint**
- `PUT /api/profiles/{name}/model` **Update Profile Model Endpoint** — Set the main model for a specific profile's config.yaml without touching the dashboard's
- `POST /api/profiles/{name}/open-terminal` **Open Profile Terminal Endpoint**
- `GET /api/profiles/{name}/setup-command` **Get Profile Setup Command**
- `GET /api/profiles/{name}/soul` **Get Profile Soul**
- `PUT /api/profiles/{name}/soul` **Update Profile Soul**

### providers
- `GET /api/providers/custom-endpoints` **List Custom Endpoints** — Return configured OpenAI-compatible custom endpoints for Desktop.
- `POST /api/providers/custom-endpoints` **Upsert Custom Endpoint** — Create or update a v12+ ``providers`` custom endpoint entry.
- `POST /api/providers/custom-endpoints/validate` **Validate Custom Endpoint** — Probe a custom endpoint by calling its OpenAI-compatible /models URL.
- `DELETE /api/providers/custom-endpoints/{endpoint_id}` **Delete Custom Endpoint** — Remove a configured custom endpoint from ``providers``.
- `POST /api/providers/custom-endpoints/{endpoint_id}/activate` **Activate Custom Endpoint** — Set a configured custom endpoint as the default model provider.
- `GET /api/providers/oauth` **List Oauth Providers** — Every OAuth-capable provider with current status (token_preview is the last
- `DELETE /api/providers/oauth/sessions/{session_id}` **Cancel Oauth Session** — Cancel a pending OAuth session. Token-protected.
- `DELETE /api/providers/oauth/{provider_id}` **Disconnect Oauth Provider** — Disconnect an OAuth provider. Token-protected (matches /env/reveal).
- `GET /api/providers/oauth/{provider_id}/poll/{session_id}` **Poll Oauth Session** — Poll a session's status (no auth — read-only state). One endpoint serves
- `POST /api/providers/oauth/{provider_id}/start` **Start Oauth Login** — Initiate an OAuth login flow. Token-protected.
- `POST /api/providers/oauth/{provider_id}/submit` **Submit Oauth Code** — Submit the auth code for PKCE flows. Token-protected.
- `POST /api/providers/validate` **Validate Provider Credential** — Live-probe a provider credential before it's saved.

### sessions
- `GET /api/sessions` **Get Sessions** — List sessions.
- `POST /api/sessions/bulk-delete` **Bulk Delete Sessions Endpoint** — Delete every session in ``body.ids`` in one transaction (POST: many
- `DELETE /api/sessions/empty` **Delete Empty Sessions Endpoint** — Delete every empty, ended, non-archived session in one transaction.
- `GET /api/sessions/empty/count` **Count Empty Sessions Endpoint** — Count of empty, ended, non-archived sessions (the "Delete empty (N)" button).
- `POST /api/sessions/import` **Import Sessions Endpoint** — Import sessions exported from the dashboard or CLI (session rows only —
- `POST /api/sessions/owner-backfill` **Backfill Session Owner Profiles** — Stamp legacy ``profile_name = NULL`` rows with the serving-profile identity.
- `POST /api/sessions/prune` **Prune Sessions Endpoint** — Delete ended sessions matching filters without blocking the event loop.
- `GET /api/sessions/search` **Search Sessions** — Search sessions by ID (first) plus FTS5 message content.
- `GET /api/sessions/stats` **Get Session Stats** — Session-store statistics (mirrors `hermes sessions stats`).
- `GET /api/sessions/{session_id}` **Get Session Detail**
- `DELETE /api/sessions/{session_id}` **Delete Session Endpoint**
- `PATCH /api/sessions/{session_id}` **Rename Session Endpoint** — Update ``title`` (empty clears) and/or the flags; ``pinned`` exempts from
- `GET /api/sessions/{session_id}/export` **Export Session Endpoint** — Stream a single session (metadata + messages) as JSON.
- `GET /api/sessions/{session_id}/latest-descendant` **Get Session Latest Descendant**
- `GET /api/sessions/{session_id}/messages` **Get Session Messages**

### skills
- `GET /api/skills` **Get Skills**
- `POST /api/skills` **Create Skill** — Create a skill via the agent's ``skill_manage`` write path, minus the
- `GET /api/skills/content` **Get Skill Content** — Raw SKILL.md text for the dashboard editor.
- `PUT /api/skills/content` **Update Skill Content** — Replace the SKILL.md of an existing skill (full rewrite) from the editor.
- `POST /api/skills/hub/install` **Install Skill Hub**
- `GET /api/skills/hub/official` **List Official Skills** — The ENTIRE optional-skills catalog (local scan), marked installed for ``profile``.
- `GET /api/skills/hub/preview` **Preview Skill Hub** — A hub skill's SKILL.md + file manifest WITHOUT installing it; scoped to
- `GET /api/skills/hub/scan` **Scan Skill Hub** — Install-time security scan of a hub skill WITHOUT installing it (the CLI's
- `GET /api/skills/hub/search` **Search Skills Hub** — Search the skill hub across all configured sources (network-bound).
- `GET /api/skills/hub/sources` **List Skills Hub Sources** — Configured skill-hub sources + installed-skill provenance (scoped to
- `POST /api/skills/hub/uninstall` **Uninstall Skill Hub**
- `POST /api/skills/hub/update` **Update Skills Hub**
- `PUT /api/skills/toggle` **Toggle Skill**

### ssh
- `GET /api/ssh/ownership` **Get Ssh Ownership**

### status
- `GET /api/status` **Get Status** — Public machine-level liveness probe (``PUBLIC_API_PATHS``): version, gateway state,

### system
- `GET /api/system/stats` **Get System Stats** — Host + process system stats for the System page (stdlib identity; psutil CPU/memory/

### tools
- `POST /api/tools/computer-use/permissions/grant` **Grant Computer Use Permissions** — Spawn ``hermes computer-use permissions grant`` (macOS-only: launches
- `GET /api/tools/computer-use/status` **Get Computer Use Status** — Computer Use readiness for the desktop card (payload shape: see
- `PUT /api/tools/terminal/backend` **Select Terminal Backend** — Persist ``terminal.backend``.  A backend that still needs setup is
- `GET /api/tools/terminal/backends` **Get Terminal Backends** — Terminal backend rows with health probes: ``status`` is ``ready`` /
- `GET /api/tools/toolsets` **Get Toolsets**
- `PUT /api/tools/toolsets/{name}` **Toggle Toolset** — Enable/disable a configurable toolset for its configuration platform
- `GET /api/tools/toolsets/{name}/config` **Get Toolset Config** — Provider matrix + key status for a toolset's config panel (the CLI picker
- `PUT /api/tools/toolsets/{name}/env` **Save Toolset Env** — Persist API keys to ``.env`` via ``save_env_value``.  Keys are validated
- `PUT /api/tools/toolsets/{name}/model` **Select Toolset Model** — Persist a backend model selection (``image_gen.model`` /
- `GET /api/tools/toolsets/{name}/models` **Get Toolset Models** — Model catalog for a toolset backend (image/video gen) — the GUI
- `POST /api/tools/toolsets/{name}/post-setup` **Run Toolset Post Setup** — Spawn ``hermes tools post-setup <key>`` (long-running installs) as a
- `PUT /api/tools/toolsets/{name}/provider` **Select Toolset Provider** — Persist a provider selection via ``apply_provider_selection`` (shared with

### webhooks
- `GET /api/webhooks` **List Webhooks**
- `POST /api/webhooks` **Create Webhook**
- `POST /api/webhooks/enable` **Enable Webhooks**
- `DELETE /api/webhooks/{name}` **Delete Webhook**
- `PUT /api/webhooks/{name}/enabled` **Set Webhook Enabled** — Disabled routes stay on disk (re-enable later) but the gateway rejects

### {full_path}
- `GET /{full_path}` **Serve Spa**

---

## Conexões

- [[wiki/systems/hermes.md]]
- [[wiki/systems/hermes-estado.md]]
- [[wiki/systems/vps.md]]
