---
name: openclaw-admin
description: Use when diagnosing, configuring, fixing, tuning, or setting up anything in OpenClaw — gateway not responding, channel silent, model failover issues, auth errors, agent routing problems, config validation failures, or any operational task involving openclaw.json, the gateway, agents, channels, or the openclaw CLI.
---

# OpenClaw Admin

Operate, diagnose, configure, and fix OpenClaw installations. You have direct filesystem access — use it to read config, search docs, and make safe edits.

## Key Paths

| What | Path |
|------|------|
| **Config** | `~/.openclaw/openclaw.json` (JSON5 — comments + trailing commas OK) |
| **Env vars** | `~/.openclaw/.env` |
| **Secrets** | `~/.openclaw/secrets.json` |
| **Agent workspaces** | `~/.openclaw/agents/<agentId>/` |
| **Sessions** | `~/.openclaw/agents/<agentId>/sessions/` |
| **Extensions** | `~/.openclaw/extensions/` (+ paths in `plugins.load.paths`) |
| **Docs (~500 files)** | `~/.npm-global/lib/node_modules/openclaw/docs/` |
| **Docs manifest** | `~/.npm-global/lib/node_modules/openclaw/docs/docs.json` |
| **CLI binary** | `~/.npm-global/bin/openclaw` |
| **Managed skills** | `~/.npm-global/lib/node_modules/openclaw/skills/` |
| **Hooks** | `~/.openclaw/hooks/` |
| **Custom scripts** | `~/.openclaw/bin/` |

## Diagnostic Ladder

Run these in order when something is broken:

```bash
openclaw status                    # channel health + recent errors
openclaw gateway status            # runtime state + RPC probe
openclaw logs --follow             # real-time log stream
openclaw doctor                    # config validation + guided repairs
openclaw channels status --probe   # per-channel connection check
```

**Healthy signals:** gateway status shows `Runtime: running` + `RPC probe: ok`. Doctor reports no blocking issues. Channels show connected/ready.

**Silent failures** (no errors in logs) almost always mean an allowlist or policy is blocking. Surface dropped messages with:
```bash
openclaw config set logging.level debug   # or trace for maximum detail
openclaw logs --follow                     # then reproduce the issue
```

## Safe Config Editing Workflow

> **AI Agent Warning**: NEVER batch-write the entire `openclaw.json` file. Always read, backup, edit individual keys, and validate. Overwriting the whole file is the #1 cause of catastrophic config loss. Use `openclaw config set <key> <value>` for individual changes when possible.

1. **Read** current config: `cat ~/.openclaw/openclaw.json` or `openclaw config get <key>`
2. **Backup**: `cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.json.bak-$(date +%Y%m%d-%H%M%S)`
3. **Edit** individual keys (or use `openclaw config set <key> <value>`)
4. **Validate**: `openclaw config validate`
5. **Restart only if needed**: most config hot-reloads automatically. See config-map.md for what requires `openclaw gateway restart`.

**Auto-repair**: `openclaw doctor --fix` writes a `.bak` backup and removes unknown keys.

## Finding the Right Doc

The docs directory has ~500 markdown files with consistent frontmatter:

```yaml
---
summary: "One-line description"
read_when:
  - "Contextual trigger for when to read this doc"
title: "Page Title"
---
```

**To find docs for a problem**, grep the `read_when` and `summary` fields:
```bash
grep -rl "your search term" ~/.npm-global/lib/node_modules/openclaw/docs/ --include="*.md"
```

**Key doc directories:**
- `cli/` — command reference (one file per command)
- `gateway/` — config reference, troubleshooting, security
- `channels/` — per-platform setup guides
- `concepts/` — architecture, sessions, models, OAuth
- `providers/` — model provider setup (Anthropic, OpenAI, etc.)
- `help/` — FAQ, troubleshooting triage
- `automation/` — cron jobs, scheduling, webhooks
- `tools/` — built-in tools (web search, TTS, media, etc.)
- `nodes/` — mobile node pairing (iOS/Android)
- `plugins/` — extension development and management
- `install/` — platform-specific deployment guides
- `platforms/` — deployment platforms (VPS, Docker, etc.)
- `security/` — threat models, security audit
- `start/` — onboarding, getting started
- `web/` — Control UI, dashboard

## Common Pitfalls

### Discord

**Dual Discord allowlists**: Discord channels must be in BOTH `channels.discord.guilds.<guildId>.channels` AND `channels.discord.accounts.<account>.guilds.<guildId>.channels`. Missing either = silent message drop. This is the #1 cause of "bot doesn't respond in new channel." Non-default accounts are especially prone — their channels often get added to the account allowlist but not the top-level guild allowlist.

**DM policy defaults**: `dmPolicy: "pairing"` (default) requires sender approval via pairing code. Unrecognized senders are silently held, not rejected.

**Group mention gating**: Groups default to `requireMention: true`. Bot won't respond unless @-mentioned or text matches `mentionPatterns`.

**Bot token contention**: Two gateways sharing the same Discord bot token cause WebSocket reconnect storms — Discord terminates the first connection when a second gateway connects with the same token. Each gateway (main, rescue, etc.) MUST use a unique bot token.

**Same-bot inter-agent messaging**: The bot ignores its own messages. Agents sharing one bot token can't trigger each other — Agent A's message appears as the bot, so Agent B's binding skips it. Fix: use multiple bot accounts so inter-agent messages appear as cross-bot communication.

**Agent-to-bot mapping is NOT 1:1**: Multiple agents can share a bot token. Always verify which bot token an agent uses (`channels.discord.accounts` in config) before making changes — don't assume each agent has its own.

### Auth & Secrets

**Model failover profile pinning**: If both OAuth and API key exist for a provider, round-robin can flip profiles unexpectedly. Fix: set `auth.order[provider]` to lock profile order. Also watch for stale `usageStats` with `errorCount` poisoning profile selection.

**OAuth write-back limitation**: OAuth refresh tokens (e.g., openai-codex) CANNOT go in 1Password because SecretRef is read-only and OAuth needs write-back. These must stay as inline tokens.

**Secrets provider errors**: The `op` binary must be owned by the current user (uid match) OR its directory must be in `trustedDirs`. Use a wrapper script (e.g., `~/.openclaw/bin/op-secrets.sh`) instead of pointing directly at `/usr/bin/op`. The wrapper also needs `passEnv: ["HOME", "OP_SERVICE_ACCOUNT_TOKEN"]` for exec providers.

**1Password rate limit exhaustion**: A crash-looping gateway with exec-based secrets burns the entire 1P account-level rate limit (1000 ops, 21-hour reset). Account-level means new service account tokens don't help. Fix: deploy a local 1Password Connect server (Docker) — the wrapper script tries Connect first, falls back to CLI.

### Gateway & Config

**AI agents destroying config**: Never batch-write the entire `openclaw.json` file. Always backup first, edit individual keys, and validate after each change. This is the #1 cause of catastrophic config loss.

**Systemd crash loop**: `Restart=always` + invalid config = infinite restart loop (one case hit 18,000 restarts). Systemd can't distinguish "crashed" from "config validation failure." Consider adding `StartLimitBurst`/`StartLimitIntervalSec` to the service unit.

**Binding validation errors**: A single invalid binding entry blocks the entire gateway from starting. Always run `openclaw config validate` after editing bindings. Dangling references to deleted agents are a common cause after agent cleanup.

**Carbon/WebSocket memory leak**: The `@buape/carbon` Discord library (beta) leaks WebSocket resources across reconnects. Multiple bot accounts multiply the leak rate. Mitigate with scheduled gateway restarts (every 2-4 hours). Awaiting upstream fix.

### Agents & Plugins

**Workspace is NOT a sandbox**: Agent workspaces at `~/.openclaw/agents/<id>/` are just the default cwd. Absolute paths escape. Enable `agents.defaults.sandbox.mode` for Docker isolation.

**Plugin duplicate loading**: Directory-level entries in `plugins.load.paths` cause sibling scanning, loading plugins multiple times. Be explicit about plugin paths — point to the specific plugin directory, not its parent.

**Cron init-time limitation**: `context.cron` is NOT available at plugin init time — only inside gateway method handlers. This affects any plugin trying to register or query cron jobs during initialization.

**Cron requires enabled**: `cron.enabled: false` silently prevents all jobs from running.

## Plugins

OpenClaw uses a plugin system for extensions. Plugins live in `~/.openclaw/extensions/` and are configured via `plugins` in config.

```bash
openclaw plugins list                      # list installed plugins
```

Key config:
- `plugins.enabled` — master toggle
- `plugins.load.paths` — additional plugin directories (be specific — directory-level paths cause sibling scanning)
- `plugins.slots.memory` / `plugins.slots.contextEngine` — slot-based plugin selection
- `plugins.entries.<id>` — per-plugin config (`enabled`, `apiKey`, `env`, hooks)
- `plugins.deny` — plugin deny list

## ACP (Agent Communication Protocol)

ACP enables agent-to-agent communication and dispatch through the gateway. The default backend is `acpx`.

Key config:
- `acp.enabled` — global gate
- `acp.backend` — runtime backend (e.g., `"acpx"`)
- `acp.defaultAgent` — fallback target agent
- `acp.allowedAgents` — allowlist of permitted agent IDs
- `acp.stream` — streaming config (coalescing, chunking, delivery mode)
- `acp.runtime` — TTL and install settings

```bash
openclaw acp                               # ACP tools
```

## Channels Beyond Discord

The skill focuses on Discord, but OpenClaw also supports:
- **Slack** — native streaming, typing reactions, exec approvals, thread session isolation
- **BlueBubbles** — iMessage bridge integration
- **Telegram** — standard chat channel
- **WhatsApp** — web transport with reconnect config (`web` config section)
- **Google Chat** — via `googlechat` config
- **Mattermost** — via plugin
- **Signal** — encrypted messaging

Each channel has its own allowlist/policy patterns similar to Discord. Check `docs/channels/` for per-platform guides.

## Internal Hooks

Internal hooks are configured under `hooks.internal`. Each can be individually enabled/disabled via `hooks.internal.<name>.enabled`. Hook source files live in `~/.openclaw/hooks/`.

Common hooks include session-memory, agent-delegate, and command-logger. List your active hooks in config under `hooks.internal`.

## Rescue Bot (Optional)

A rescue bot can run on a separate `--profile rescue` to monitor and fix the main gateway independently. See `docs/gateway/multiple-gateways.md`.

| What | Detail |
|------|--------|
| Profile | `openclaw --profile rescue` |
| Config | `~/.openclaw-rescue/openclaw.json` |
| State | `~/.openclaw-rescue/` |
| Port | Choose a port different from main (e.g., 19789) |
| Service | Install with `openclaw --profile rescue gateway install` |

Key considerations:
- Each gateway MUST use a unique Discord bot token
- The rescue agent needs filesystem access to the main gateway's config
- Keep the rescue bot's model chain independent from main

```bash
openclaw --profile rescue status          # check rescue bot health
openclaw --profile rescue gateway status  # runtime + RPC
openclaw --profile rescue cron list       # health check + backup jobs
```

## Reference Files

For CLI command lookup, read `cli-reference.md` in this skill directory.
For config key details and restart requirements, read `config-map.md` in this skill directory.
