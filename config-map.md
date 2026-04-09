# OpenClaw Config Map

Config file: `~/.openclaw/openclaw.json` (JSON5 format — comments and trailing commas allowed).

All fields are optional — OpenClaw uses safe defaults when omitted.

## Hot-Reload (no restart needed)

These changes take effect automatically when the config file is saved.

| Key | Controls |
|-----|----------|
| `agents` | Agent definitions, defaults (workspace, models, memory, sandbox, compaction, heartbeat, subagents, context pruning, media models, block streaming, typing) |
| `agents.defaults.model` | Primary model, fallbacks, per-model params, image/PDF/music/video generation models |
| `agents.defaults.models` | Model catalog — aliases, context params, transport |
| `agents.list` | Named agent array with per-agent overrides (identity, thinkingDefault, runtime, groupChat) |
| `channels` | All channel providers (Discord, Slack, Telegram, WhatsApp, Signal, BlueBubbles, Google Chat, Mattermost) |
| `channels.defaults` | Shared group policy, heartbeat, DM policy, context visibility defaults |
| `channels.discord` | Accounts, guilds, channel allowlists, voice config |
| `channels.modelByChannel` | Pin specific channel IDs to specific models |
| `bindings` | Channel-to-agent routing rules (agentId, match criteria) |
| `models` | Provider config (baseUrl, API type, catalog mode) |
| `auth` | Auth profiles, cooldown settings (per-provider), profile ordering, overloaded/rate-limited rotation |
| `tools` | Tool profiles (minimal/coding/messaging/full), allow/deny policy, exec, media, loop detection, sandbox, experimental |
| `hooks` | Internal hooks, webhooks, HTTP ingress, Gmail integration, transforms, presets |
| `cron` | Job concurrency, session retention, run logging, retry config, failure alerting |
| `session` | Scoping (per-sender/global), DM scope, thread bindings, TTL, identity links, send policy, agent-to-agent ping-pong limits, maintenance |
| `browser` | Multi-profile browser support, SSRF policy, evaluate toggle, CDP/remote profiles |
| `logging` | Log level, console style, redaction, file path, max file size, custom redaction patterns |
| `commands` | Chat commands (/model, /reset, /bash, /config) |
| `messages` | Chunk limits, streaming, TTS, response prefix templates, ack reactions, queue modes (steer/followup/collect/interrupt) |
| `env` | Environment variables, shell env loading |
| `ui` | UI preferences and theming |
| `secrets` | Secret providers (env, file, exec), trustedDirs, passEnv, mode (json/singleValue) |
| `identity` | Gateway identity metadata (now primarily configured per-agent at agents.list[].identity) |
| `acp` | Agent Communication Protocol (enabled, backend, dispatch, streaming, allowedAgents) |
| `diagnostics` | Instrumentation, OpenTelemetry export, cache traces, stuck-session warnings |
| `talk` | Voice interface (provider, ElevenLabs config, silence timeout) |
| `canvasHost` | Agent-editable HTML/CSS/JS served over gateway port |
| `nodeHost` | Node host settings (browser proxy) |
| `skills` | Skill management (allowBundled, load dirs, install preferences, per-skill config) |
| `web` | WhatsApp/Baileys web transport (reconnect, heartbeat) |
| `meta` | Auto-managed version tracking (read-only, written by OpenClaw) |
| `wizard` | Setup wizard state (read-only, written by CLI) |
| `update` | Release channel (stable/beta/dev), auto-update settings |

## Restart Required

These changes require `openclaw gateway restart` to take effect.

| Key | Controls |
|-----|----------|
| `gateway.port` | Listening port (default: 18789, dev: 19001) |
| `gateway.bind` | Bind address |
| `gateway.auth` | Auth mode and scheme |
| `gateway.tls` | HTTPS/TLS certificates (auto-generate, certPath, keyPath, caPath) |
| `gateway.tailscale` | Tailscale integration |
| `gateway.http` | HTTP server config, OpenAI-compatible endpoints (chatCompletions, responses) |
| `gateway.reload` | Hot-reload mode itself (hybrid/hot/restart/off) |
| `discovery` | mDNS advertising, health endpoints |
| `plugins` | Extension loading, slots (memory/contextEngine), per-plugin config, deny list, install metadata |
| `gateway.controlUi` | Control UI (enabled, basePath, allowedOrigins, auth) |
| `gateway.remote` | Remote gateway connection (url, transport, token) |
| `gateway.push` | APNs push notification relay |

**Removed:** `bridge` — TCP bridge is gone; nodes connect over Gateway WebSocket. `bridge.*` keys cause validation failure.

## Hot-Reload Modes

Set via `gateway.reload`:

| Mode | Behavior |
|------|----------|
| `hybrid` (default) | Auto-applies safe changes, auto-restarts for critical ones |
| `hot` | Applies safe changes only, warns when restart needed |
| `restart` | Restarts on any config change |
| `off` | No file watching |

## Config Syntax Features

**JSON5**: Comments (`//` and `/* */`), trailing commas, unquoted keys, single-quoted strings.

**Env var references**: `${VAR_NAME}` in string values. Uppercase only. Missing var = startup error.

**Config includes**: `$include: "./file.json5"` or `$include: ["a.json5", "b.json5"]` (array = deep merge, later wins). Up to 10 levels deep.

**Strict validation**: Unknown keys or bad types cause startup failure. Fix with `openclaw doctor --fix`.

## Key Config Patterns

**Model failover chain:**
```json5
{
  agents: {
    defaults: {
      model: {
        primary: "anthropic/claude-opus-4-6",
        fallbacks: ["anthropic/claude-sonnet-4-5", "openrouter/anthropic/claude-sonnet-4-5"]
      }
    }
  }
}
```

**Channel-to-agent binding:**
```json5
{
  bindings: [
    {
      agentId: "dev",
      match: { channel: "discord", peer: { kind: "channel", id: "<channel-id>" }, guildId: "<guild-id>" }
    }
  ]
}
```

**Discord channel allowlist (BOTH locations required):**
```json5
{
  channels: {
    discord: {
      // Location A: guild-level
      guilds: { "<guildId>": { channels: { "<channelId>": { allow: true, requireMention: false } } } },
      // Location B: account-level
      accounts: { "default": { guilds: { "<guildId>": { channels: { "<channelId>": { allow: true, requireMention: false } } } } } }
    }
  }
}
```

**Auth profile ordering (prevents round-robin flipping):**
```json5
{
  auth: {
    order: { anthropic: ["anthropic:manual", "anthropic:oauth"] }
  }
}
```

**1Password Connect secrets provider:**
```json5
{
  secrets: {
    providers: {
      "my-secrets": {
        source: "exec",
        command: "<path-to-your-secrets-wrapper>",
        passEnv: ["HOME", "OP_SERVICE_ACCOUNT_TOKEN", "OP_CONNECT_TOKEN", "OP_CONNECT_HOST", "OP_CONNECT_VAULT"],
        trustedDirs: ["<directory-containing-wrapper>"],
        mode: "json"
      }
    }
  }
}
```

**SecretRef usage (inline credential references):**
```json5
{
  channels: {
    discord: {
      accounts: {
        default: {
          token: { source: "exec", provider: "my-secrets", id: "<1p-item-id>" }
        }
      }
    }
  }
}
```

**ACP (Agent Communication Protocol):**
```json5
{
  acp: {
    enabled: true,
    backend: "acpx",
    defaultAgent: "claude",
    allowedAgents: ["main", "dev", "pm", "ops"],
    stream: { coalesceIdleMs: 500, maxChunkChars: 4000 },
    runtime: { ttlMinutes: 120 }
  }
}
```

**Diagnostics with OpenTelemetry:**
```json5
{
  diagnostics: {
    enabled: true,
    cacheTrace: { enabled: true },
    otel: {
      enabled: true,
      endpoint: "http://localhost:4318",
      protocol: "http",
      serviceName: "openclaw-gateway"
    }
  }
}
```
