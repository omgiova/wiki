---
type: system
tags: [hermes, api, rest, openapi]
title: Hermes Agent — API REST (OpenAPI)
description: Referência completa dos endpoints REST do Hermes Agent, gerada automaticamente do /openapi.json — seção Interface do sistema Hermes
timestamp: 2026-07-20T03:00:01-03:00
status: stable
---

# Hermes Agent — API REST

> **Gerado automaticamente** a partir de `GET /openapi.json` (Hermes Agent v0.18.0, OAS 3.1).
> Para atualizar manualmente: execute `/root/scripts/update-hermes-wiki.sh`
> Última atualização: 2026-07-20T03:00:01-03:00 | Total de endpoints: 212

- **Base URL:** `http://localhost:9119`
- **Auth:** POST `/auth/password-login` com `{"username":"...","password":"...","provider":"basic"}`
- **Docs interativas:** `http://localhost:9119/docs`

---

## Endpoints por grupo

### actions
- `GET /api/actions/{name}/status` **Get Action Status** — Tail an action log and report whether the process is still running.

### analytics
- `GET /api/analytics/models` **Get Models Analytics** — Rich per-model analytics for the Models dashboard page.
- `GET /api/analytics/usage` **Get Usage Analytics**

### assets
- `GET /assets/{filename}.css` **Serve Css**

### audio
- `GET /api/audio/elevenlabs/voices` **Get Elevenlabs Voices** — Return ElevenLabs voices when an API key is configured.
- `POST /api/audio/speak` **Speak Text** — Synthesize speech and return audio as base64 data URL.
- `POST /api/audio/transcribe` **Transcribe Audio Upload**

### auth
- `GET /api/auth/me` **Auth Me** — Return the verified session as JSON. Auth-required (gate enforces).
- `GET /api/auth/providers` **Auth Providers**
- `POST /api/auth/ws-ticket` **Auth Ws Ticket** — Mint a short-lived single-use ticket for the authenticated session.
- `GET /auth/callback` **Auth Callback**
- `GET /auth/login` **Auth Login**
- `POST /auth/logout` **Auth Logout**
- `POST /auth/password-login` **Auth Password Login** — Authenticate a username/password against a password provider.

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
- `DELETE /api/credentials/pool/{provider}/{index}` **Remove Credential Pool Entry** — Remove a pool entry.  ``index`` is 1-based (matches the list response).

### cron
- `GET /api/cron/blueprints` **List Cron Blueprints** — Return the blueprint catalog as form schemas for the dashboard gallery.
- `POST /api/cron/blueprints/instantiate` **Instantiate Blueprint** — Fill a blueprint's slots and create the cron job (form-submit path).
- `GET /api/cron/delivery-targets` **Get Cron Delivery Targets** — Delivery targets the cron dropdown should offer.
- `POST /api/cron/fire` **Cron Fire Webhook** — Chronos managed-cron fire webhook (NAS -> agent).
- `GET /api/cron/jobs` **List Cron Jobs**
- `POST /api/cron/jobs` **Create Cron Job**
- `GET /api/cron/jobs/{job_id}` **Get Cron Job**
- `PUT /api/cron/jobs/{job_id}` **Update Cron Job**
- `DELETE /api/cron/jobs/{job_id}` **Delete Cron Job**
- `POST /api/cron/jobs/{job_id}/pause` **Pause Cron Job**
- `POST /api/cron/jobs/{job_id}/resume` **Resume Cron Job**
- `GET /api/cron/jobs/{job_id}/runs` **List Cron Job Runs** — Run sessions produced by a cron job, newest first.
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
- `PUT /api/dashboard/font` **Set Dashboard Font** — Set the dashboard font override (persists to config.yaml).
- `PUT /api/dashboard/plugin-providers` **Put Plugin Providers** — Persist memory provider / context engine selection (writes config.yaml).
- `GET /api/dashboard/plugins` **Get Dashboard Plugins** — Return discovered dashboard plugins (excludes user-hidden and non-enabled ones).
- `GET /api/dashboard/plugins/hub` **Get Plugins Hub** — Unified agent plugins + dashboard extension metadata (session protected).
- `GET /api/dashboard/plugins/rescan` **Rescan Dashboard Plugins** — Force re-scan of dashboard plugins.
- `POST /api/dashboard/plugins/{name}/visibility` **Post Plugin Visibility** — Toggle a plugin's sidebar visibility (persists to config.yaml dashboard.hidden_plugins).
- `PUT /api/dashboard/theme` **Set Dashboard Theme** — Set the active dashboard theme (persists to config.yaml).
- `GET /api/dashboard/themes` **Get Dashboard Themes** — Return available themes and the currently active one.

### dashboard-plugins
- `GET /dashboard-plugins/{plugin_name}/{file_path}` **Serve Plugin Asset** — Serve static assets from a dashboard plugin directory.

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
- `POST /api/files/upload` **Upload Managed File**
- `POST /api/files/upload-stream` **Upload Managed File Stream**

### fs
- `GET /api/fs/default-cwd` **Fs Default Cwd**
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
- `POST /api/git/branch/switch` **Git Branch Switch Route**
- `GET /api/git/branches` **Git Branches Route**
- `GET /api/git/file-diff` **Git File Diff Route**
- `POST /api/git/review/commit` **Git Commit Route**
- `GET /api/git/review/commit-context` **Git Commit Context Route**
- `POST /api/git/review/create-pr` **Git Create Pr Route**
- `GET /api/git/review/diff` **Git Review Diff Route**
- `GET /api/git/review/list` **Git Review List Route**
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

### hermes
- `POST /api/hermes/update` **Update Hermes** — Kick off ``hermes update`` in the background.
- `GET /api/hermes/update/check` **Check Hermes Update** — Report whether a Hermes update is available, without applying it.

### learning
- `GET /api/learning/graph` **Get Learning Graph** — Learning graph payload for the desktop panel.
- `GET /api/learning/node` **Get Learning Node** — Current content of a journey node (skill SKILL.md or memory chunk), for an edit prefill.
- `DELETE /api/learning/node` **Delete Learning Node** — Delete a journey node — skills are archived (restorable), memories removed.
- `PUT /api/learning/node` **Update Learning Node** — Rewrite a journey node's content (SKILL.md or memory chunk).

### login
- `GET /login` **Login Page**

### logs
- `GET /api/logs` **Get Logs**

### mcp
- `GET /api/mcp/catalog` **List Mcp Catalog** — Browse the Nous-approved MCP catalog (the optional-mcps/ manifests).
- `POST /api/mcp/catalog/install` **Install Mcp Catalog Entry** — Install a catalog MCP into config.yaml.
- `GET /api/mcp/servers` **List Mcp Servers**
- `POST /api/mcp/servers` **Add Mcp Server**
- `DELETE /api/mcp/servers/{name}` **Remove Mcp Server**
- `PUT /api/mcp/servers/{name}/enabled` **Set Mcp Server Enabled** — Enable or disable an MCP server (takes effect on next session/gateway).
- `POST /api/mcp/servers/{name}/test` **Test Mcp Server** — Connect to the server, list its tools, disconnect.  Returns tool list.

### media
- `GET /api/media` **Get Media** — Return a gateway-local image file as a base64 data URL.

### memory
- `GET /api/memory` **Get Memory Status**
- `PUT /api/memory/provider` **Set Memory Provider**
- `GET /api/memory/providers/{name}/config` **Get Memory Provider Config**
- `PUT /api/memory/providers/{name}/config` **Update Memory Provider Config**
- `POST /api/memory/providers/{provider}/oauth/start` **Start Memory Oauth** — Begin a provider's zero-CLI OAuth flow — opens the browser and captures
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

### model
- `GET /api/model/auxiliary` **Get Auxiliary Models** — Return current auxiliary task assignments.
- `GET /api/model/info` **Get Model Info** — Return resolved model metadata for the currently configured model.
- `GET /api/model/moa` **Get Moa Models** — Return the configured Mixture-of-Agents provider/model slots.
- `PUT /api/model/moa` **Set Moa Models** — Persist the Mixture-of-Agents provider/model slots.
- `GET /api/model/options` **Get Model Options** — Return authenticated providers + their curated model lists.
- `GET /api/model/recommended-default` **Get Recommended Default Model** — Return the recommended default model for a freshly-authenticated provider.
- `POST /api/model/set` **Set Model Assignment** — Assign a model to the main slot or an auxiliary task slot.

### ops
- `POST /api/ops/backup` **Run Backup**
- `GET /api/ops/backup/download` **Download Dashboard Backup**
- `GET /api/ops/checkpoints` **List Checkpoints** — List the /rollback shadow store checkpoints (read-only).
- `POST /api/ops/checkpoints/prune` **Prune Checkpoints**
- `POST /api/ops/config-migrate` **Run Config Migrate**
- `POST /api/ops/debug-share` **Run Debug Share Endpoint** — Upload a redacted debug report + full logs and return the paste URLs.
- `POST /api/ops/doctor` **Run Doctor**
- `POST /api/ops/dump` **Run Dump**
- `GET /api/ops/hooks` **List Hooks** — List configured shell hooks from config.yaml with consent + health.
- `POST /api/ops/hooks` **Create Hook** — Add a shell hook to config.yaml (and optionally approve it).
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
- `GET /api/profiles/active` **Get Active Profile Endpoint** — Return the sticky active profile and the profile this dashboard
- `POST /api/profiles/active` **Set Active Profile Endpoint** — Set the sticky active profile (mirrors ``hermes profile use``).
- `GET /api/profiles/sessions` **Get Profiles Sessions** — Unified, read-only session list aggregated across ALL profiles.
- `PATCH /api/profiles/{name}` **Rename Profile Endpoint**
- `DELETE /api/profiles/{name}` **Delete Profile Endpoint** — Delete a profile. The dashboard collects the user's confirmation in
- `POST /api/profiles/{name}/describe-auto` **Describe Profile Auto Endpoint** — Auto-generate a profile's description via the auxiliary LLM
- `PUT /api/profiles/{name}/description` **Update Profile Description Endpoint** — Set or clear a profile's role description (kanban routing signal).
- `PUT /api/profiles/{name}/model` **Update Profile Model Endpoint** — Set the main model (``model.default`` + ``model.provider``) for a
- `POST /api/profiles/{name}/open-terminal` **Open Profile Terminal Endpoint**
- `GET /api/profiles/{name}/setup-command` **Get Profile Setup Command**
- `GET /api/profiles/{name}/soul` **Get Profile Soul**
- `PUT /api/profiles/{name}/soul` **Update Profile Soul**

### providers
- `GET /api/providers/oauth` **List Oauth Providers** — Enumerate every OAuth-capable LLM provider with current status.
- `DELETE /api/providers/oauth/sessions/{session_id}` **Cancel Oauth Session** — Cancel a pending OAuth session. Token-protected.
- `DELETE /api/providers/oauth/{provider_id}` **Disconnect Oauth Provider** — Disconnect an OAuth provider. Token-protected (matches /env/reveal).
- `GET /api/providers/oauth/{provider_id}/poll/{session_id}` **Poll Oauth Session** — Poll a session's status (no auth — read-only state).
- `POST /api/providers/oauth/{provider_id}/start` **Start Oauth Login** — Initiate an OAuth login flow. Token-protected.
- `POST /api/providers/oauth/{provider_id}/submit` **Submit Oauth Code** — Submit the auth code for PKCE flows. Token-protected.
- `POST /api/providers/validate` **Validate Provider Credential** — Live-probe a provider credential before it's saved.

### sessions
- `GET /api/sessions` **Get Sessions** — List sessions.
- `POST /api/sessions/bulk-delete` **Bulk Delete Sessions Endpoint** — Delete every session in ``body.ids`` in a single DB transaction.
- `DELETE /api/sessions/empty` **Delete Empty Sessions Endpoint** — Delete every empty (``message_count == 0``), ended,
- `GET /api/sessions/empty/count` **Count Empty Sessions Endpoint** — Return the number of empty, ended, non-archived sessions.
- `POST /api/sessions/prune` **Prune Sessions Endpoint** — Delete ended sessions older than N days (mirrors `hermes sessions prune`).
- `GET /api/sessions/search` **Search Sessions** — Search sessions by ID plus full-text message content using FTS5.
- `GET /api/sessions/stats` **Get Session Stats** — Session-store statistics for the Sessions page (mirrors `hermes sessions stats`).
- `GET /api/sessions/{session_id}` **Get Session Detail**
- `DELETE /api/sessions/{session_id}` **Delete Session Endpoint**
- `PATCH /api/sessions/{session_id}` **Rename Session Endpoint** — Update a session: rename (or clear its title) and/or archive it.
- `GET /api/sessions/{session_id}/export` **Export Session Endpoint** — Export a single session (metadata + messages) as JSON.
- `GET /api/sessions/{session_id}/latest-descendant` **Get Session Latest Descendant**
- `GET /api/sessions/{session_id}/messages` **Get Session Messages**

### skills
- `GET /api/skills` **Get Skills**
- `POST /api/skills` **Create Skill** — Create a new custom skill (SKILL.md) from the dashboard editor.
- `GET /api/skills/content` **Get Skill Content** — Return the raw SKILL.md text for a skill, for the dashboard editor.
- `PUT /api/skills/content` **Update Skill Content** — Replace the SKILL.md of an existing skill (full rewrite) from the editor.
- `POST /api/skills/hub/install` **Install Skill Hub**
- `GET /api/skills/hub/preview` **Preview Skill Hub** — Fetch a hub skill's SKILL.md content + metadata for in-dashboard reading.
- `GET /api/skills/hub/scan` **Scan Skill Hub** — Run the install-time security scan on a hub skill WITHOUT installing it.
- `GET /api/skills/hub/search` **Search Skills Hub** — Search the skill hub across all configured sources.
- `GET /api/skills/hub/sources` **List Skills Hub Sources** — List the configured skill-hub sources and installed-skill provenance.
- `POST /api/skills/hub/uninstall` **Uninstall Skill Hub**
- `POST /api/skills/hub/update` **Update Skills Hub**
- `PUT /api/skills/toggle` **Toggle Skill**

### status
- `GET /api/status` **Get Status**

### system
- `GET /api/system/stats` **Get System Stats** — Host + process system stats for the System page.

### tools
- `POST /api/tools/computer-use/permissions/grant` **Grant Computer Use Permissions** — Spawn ``hermes computer-use permissions grant`` as a background action.
- `GET /api/tools/computer-use/status` **Get Computer Use Status** — Cross-platform Computer Use readiness for the desktop card.
- `GET /api/tools/toolsets` **Get Toolsets**
- `PUT /api/tools/toolsets/{name}` **Toggle Toolset** — Enable/disable a configurable toolset for the desktop (cli) platform.
- `GET /api/tools/toolsets/{name}/config` **Get Toolset Config** — Return the provider matrix + key status for a toolset's config panel.
- `PUT /api/tools/toolsets/{name}/env` **Save Toolset Env** — Persist API keys for a toolset's provider env vars.
- `POST /api/tools/toolsets/{name}/post-setup` **Run Toolset Post Setup** — Spawn a provider's post-setup install hook as a background action.
- `PUT /api/tools/toolsets/{name}/provider` **Select Toolset Provider** — Persist a provider selection for a toolset (no key prompting).

### webhooks
- `GET /api/webhooks` **List Webhooks**
- `POST /api/webhooks` **Create Webhook**
- `POST /api/webhooks/enable` **Enable Webhooks**
- `DELETE /api/webhooks/{name}` **Delete Webhook**
- `PUT /api/webhooks/{name}/enabled` **Set Webhook Enabled** — Enable or disable a webhook route.

### {full_path}
- `GET /{full_path}` **Serve Spa**

---

## Conexões

- [[wiki/systems/hermes.md]]
- [[wiki/systems/hermes-estado.md]]
- [[wiki/systems/vps.md]]
