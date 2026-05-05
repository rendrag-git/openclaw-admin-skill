# Local OpenClaw Install Profile

Status: uninitialized template
Last reviewed: never

This file is the only intentionally local/customized reference in the `openclaw-admin` skill. Keep `SKILL.md` generic; record host-specific install facts here after first-run discovery and update this file whenever the OpenClaw install shape changes.

Do not record secrets, tokens, OAuth material, full env-file contents, session bodies, agent workspace contents, or private conversation text. Paths, service names, ports, package versions, profile names, and redacted config-key names are okay.

Before publishing, sharing, or upstreaming a skill bundle, reset this file to the generic template or remove private host details.

## How Agents Should Use This File

1. Read this file before non-trivial OpenClaw operations.
2. If `Status` is `uninitialized template`, missing, or obviously stale, build or refresh it from read-only discovery commands before making operational decisions.
3. If discovery shows a different install shape than this file records, pause before writes/restarts and update this file with the new non-secret facts.
4. After any install migration, service-manager change, profile move, package-source change, or port/config path change, update this file in the same turn.
5. Treat this file as operator memory, not as proof of current state. Verify live state before claiming health or applying fixes.

## First-Run Build Checklist

Use read-only commands and local inspection. Pick only the checks that apply to the host.

```bash
hostname -s
uname -a
which openclaw
openclaw --version
openclaw config file
openclaw gateway status
openclaw status --deep
openclaw plugins list --json
openclaw channels status --deep
```

Service-manager probes:

```bash
ps -ef | rg openclaw
ss -ltnp | rg '18789|19001|openclaw'
systemctl list-units '*openclaw*' --no-pager
launchctl list | rg -i openclaw
docker ps --format '{{.Names}} {{.Image}} {{.Ports}}' | rg -i openclaw
```

Only run service-manager probes that exist on the current platform. Do not open env or secrets files while filling this profile.

## Maintenance Triggers

Refresh this file when any of these changes:

- OpenClaw moves to a different host, container, user, or service account.
- Package source changes, such as npm global to package manager, container image, source checkout, or release symlink.
- The active profile, config path, state directory, or gateway port changes.
- The service manager changes, such as foreground process to systemd, launchd, Docker/Compose, supervisor, tmux, cron/autopilot, or container service.
- Plugin roots, bundled/external plugin strategy, or update channel changes.
- A rescue gateway, secondary profile, or additional OpenClaw instance is added or removed.

## Install Summary

Replace the template values below on first run.

| Field | Value |
|---|---|
| Primary host | unknown |
| Host access pattern | local shell |
| Host type | unknown |
| Container or VM layer | none known |
| Install type | unknown |
| Package source | unknown |
| OpenClaw version last observed | unknown |
| CLI path | unknown |
| Node/runtime path | unknown |
| Service manager | unknown |
| Service name/container name | unknown |
| Active user/service account | unknown |
| Primary profile | default |
| Config path | unknown |
| State directory | unknown |
| Gateway bind/port | unknown |
| Health endpoint | unknown |
| Local docs path | unknown |
| Managed skills path | unknown |
| Plugin roots | unknown |
| Update channel | unknown |
| Last verification command set | unknown |

## Profiles And Instances

List every known OpenClaw profile or instance. Add rows for rescue/secondary gateways.

| Profile/instance | User | Config path | Service/process | Port | Role | Notes |
|---|---|---|---|---|---|---|
| default | unknown | unknown | unknown | unknown | primary | Fill on first run. |

## Service Reality Checks

Record the commands that are valid for this install. Use these as the starting point for future verification.

| Check | Command |
|---|---|
| CLI version | `openclaw --version` |
| Gateway status | `openclaw gateway status` |
| Runtime status | `openclaw status --deep` |
| Config validation | `openclaw doctor --non-interactive --no-workspace-suggestions` |
| Plugin health | `openclaw plugins doctor` |
| Channel health | `openclaw channels status --deep` |
| Task ledger | `openclaw tasks audit` |
| Service manager | unknown |
| Listener | unknown |
| Logs | `openclaw logs` |

## Package And Plugin Notes

Record non-secret package/plugin facts that affect updates:

- Bundled plugin strategy: unknown
- External plugin roots: unknown
- Known pinned plugins: unknown
- Known plugin install registry path: unknown
- Update command used for host package: unknown
- Update command used for plugins: unknown
- Rollback path or prior package source: unknown

## Local Safety Notes

Add local constraints that future agents must respect:

- Env files to reference but not open: unknown
- Secrets providers in use, by type/name only: unknown
- Restart approval requirement: ask before restart/stop/service reinstall
- Backup location or convention for config edits: default timestamped `.bak`
- Destructive cleanup constraints: ask before delete, move, uninstall, or service-manager changes

## Change Log

Append non-secret install-shape changes here.

| Date | Change | Verification |
|---|---|---|
| never | Template created. | Not yet built. |
