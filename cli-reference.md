# OpenClaw CLI Reference

All commands support `--help` for full flags and options. Global flags: `--dev` (dev profile), `--profile <name>` (named profile), `--log-level <level>`, `--no-color`.

## Diagnosis & Health

| Command | What it does |
|---------|-------------|
| `openclaw status` | Channel health, recent recipients, error summary |
| `openclaw status --all` | Full status report |
| `openclaw health` | Fetch health from running gateway |
| `openclaw doctor` | Config validation + guided repairs |
| `openclaw doctor --fix` | Auto-repair safe issues (writes .bak backup) |
| `openclaw doctor --deep` | Live gateway probe + extended checks |
| `openclaw logs` | Tail gateway file logs |
| `openclaw logs --follow` | Stream logs in real-time |
| `openclaw channels status --probe` | Per-channel connection check |

## Configuration

| Command | What it does |
|---------|-------------|
| `openclaw config get <key>` | Read a config value (dot-notation) |
| `openclaw config set <key> <val>` | Write a config value |
| `openclaw config unset <key>` | Remove a config key |
| `openclaw config file` | Print config file path |
| `openclaw config validate` | Validate config syntax and schema |
| `openclaw configure` | Interactive setup wizard |
| `openclaw configure --section <name>` | Configure a specific section (model, channel, etc.) |

## Gateway

| Command | What it does |
|---------|-------------|
| `openclaw gateway start` | Start gateway process |
| `openclaw gateway stop` | Stop gateway process |
| `openclaw gateway restart` | Restart gateway (required for port/auth/TLS changes) |
| `openclaw gateway status` | Runtime state, PID, RPC probe result |
| `openclaw gateway install` | Install as system service (systemd/launchd) |
| `openclaw gateway uninstall` | Remove system service |

Legacy aliases: `openclaw daemon` (alias for gateway commands), `openclaw clawbot` (legacy command aliases).

## Agents

| Command | What it does |
|---------|-------------|
| `openclaw agents list` | List all configured agents |
| `openclaw agents show <id>` | Show agent details (workspace, model, auth) |
| `openclaw agents create <id>` | Create a new agent workspace |
| `openclaw agent --agent <id> --message "text"` | Send a one-shot message to an agent |
| `openclaw sessions list` | List stored conversation sessions |
| `openclaw sessions list --agent <id>` | Sessions for a specific agent |

## Channels

| Command | What it does |
|---------|-------------|
| `openclaw channels status` | Connection state of all channels |
| `openclaw channels status --probe` | Active connection probe per channel |
| `openclaw channels list` | List configured channel providers |

## Models

| Command | What it does |
|---------|-------------|
| `openclaw models list` | Show available models |
| `openclaw models status` | Model availability and current selections |
| `openclaw models scan` | Discover models from configured providers |

## Messages

| Command | What it does |
|---------|-------------|
| `openclaw message send <dest> "text"` | Send a message to a channel/agent |
| `openclaw message read <source>` | Read recent messages |
| `openclaw message search <query>` | Search message history |

## Cron & Automation

| Command | What it does |
|---------|-------------|
| `openclaw cron list` | Show active scheduled jobs |
| `openclaw cron list --all` | Include disabled jobs |
| `openclaw cron add --name <n> --agent <a> --message "text"` | Create a job |
| `openclaw cron edit <id>` | Modify a job |
| `openclaw cron rm <id>` | Delete a job |
| `openclaw cron run <id>` | Trigger a job immediately (for testing) |
| `openclaw cron runs` | Show recent job run history |
| `openclaw cron status` | Scheduler state |
| `openclaw webhooks list` | List webhook endpoints |

## Security & Maintenance

| Command | What it does |
|---------|-------------|
| `openclaw security audit` | Security config audit |
| `openclaw security audit --deep` | Extended security scan |
| `openclaw backup create` | Create local backup archive |
| `openclaw backup verify` | Verify backup integrity |
| `openclaw update` | Update OpenClaw to latest |
| `openclaw update --channel` | Check/set update channel (stable/beta/dev) |
| `openclaw uninstall` | Remove gateway service + local data |

## ACP & Dispatch

| Command | What it does |
|---------|-------------|
| `openclaw acp` | Agent Communication Protocol tools |
| `openclaw approvals` | Manage exec approvals |

## Devices & Nodes

| Command | What it does |
|---------|-------------|
| `openclaw devices` | Device pairing + token management |
| `openclaw node` | Run headless node host service |
| `openclaw nodes` | Manage gateway-owned node pairing |

## Networking

| Command | What it does |
|---------|-------------|
| `openclaw dns` | DNS helpers (Tailscale + CoreDNS) |
| `openclaw directory` | Lookup contact/group IDs |

## Hooks & Secrets

| Command | What it does |
|---------|-------------|
| `openclaw hooks` | Manage internal agent hooks |
| `openclaw secrets` | Secrets runtime reload controls |

## Development & Debug

| Command | What it does |
|---------|-------------|
| `openclaw docs` | Search live OpenClaw docs |
| `openclaw infer` | Provider-backed inference |
| `openclaw mcp` | Manage MCP config and channel bridge |
| `openclaw qa` | QA scenarios and debugger UI |
| `openclaw system` | System events, heartbeat, presence |
| `openclaw tasks` | Inspect durable background task state |
| `openclaw tui` | Terminal UI connected to gateway |
| `openclaw webhooks` | Webhook helpers and integrations |

## Other

| Command | What it does |
|---------|-------------|
| `openclaw onboard` | Interactive onboarding wizard |
| `openclaw dashboard` | Open Control UI |
| `openclaw memory search <query>` | Search agent memory files |
| `openclaw memory reindex` | Rebuild memory search index |
| `openclaw plugins list` | List installed plugins/extensions |
| `openclaw browser status` | Browser tool status |
| `openclaw sandbox status` | Sandbox container state |
| `openclaw pairing list` | List pending/approved DM pairings |
| `openclaw pairing approve <id>` | Approve a pairing request |
| `openclaw qr` | Generate iOS pairing QR code |
| `openclaw completion` | Generate shell completion script |
| `openclaw skills list` | List available skills |
