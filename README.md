# openclaw-admin skill

An operational skill for Claude Code (or any AI coding assistant) that helps diagnose, configure, fix, and tune OpenClaw installations.

## What's included

- **SKILL.md** — Main reference: diagnostic ladder, safe config editing, common pitfalls, plugin/ACP/channel docs
- **local-install.md** — Local install profile template; built on first use and maintained when the user's install changes
- **cli-reference.md** — Complete CLI command catalog
- **config-map.md** — Config key reference with hot-reload vs restart behavior and common patterns
- **update-failure-patterns.md** — Generic install/update regression patterns for plugin drift, config drift, service-manager disagreement, task ledger issues, and channel auth failures

## Installation

Copy the skill files into your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/openclaw-admin
cp SKILL.md ~/.claude/skills/openclaw-admin/SKILL.md
cp local-install.md ~/.claude/skills/openclaw-admin/local-install.md
cp cli-reference.md ~/.claude/skills/openclaw-admin/cli-reference.md
cp config-map.md ~/.claude/skills/openclaw-admin/config-map.md
cp update-failure-patterns.md ~/.claude/skills/openclaw-admin/update-failure-patterns.md
```

On first use, the agent should fill `local-install.md` from read-only discovery commands. Keep user-specific install facts in that file rather than editing `SKILL.md`.

When publishing or sharing the skill, keep `local-install.md` as the generic template or remove private host details first.

## Skill Layout

```text
openclaw-admin/
|-- SKILL.md
|-- local-install.md
|-- cli-reference.md
|-- config-map.md
`-- update-failure-patterns.md
```

## Tested with

- OpenClaw 2026.4.x and update/debug patterns observed through 2026.5.x
- Claude Code with superpowers plugin

## Attribution

Update failure patterns are adapted from the MIT-licensed `BKF-Gitty/openclaw-update-runbook` project and generalized for non-host-specific OpenClaw installs.
