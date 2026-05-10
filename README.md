# openclaw-admin skill

An operational skill for Claude Code (or any AI coding assistant) that helps diagnose, configure, fix, and tune OpenClaw installations.

## What's included

- **SKILL.md** — Main reference: diagnostic ladder, safe config editing, common pitfalls, plugin/ACP/channel docs
- **local-install.md** — Local install profile template; built on first use and maintained when the user's install changes
- **cli-reference.md** — Complete CLI command catalog
- **config-map.md** — Config key reference with hot-reload vs restart behavior and common patterns
- **update-failure-patterns.md** — Generic install/update regression patterns for plugin drift, config drift, service-manager disagreement, task ledger issues, and channel auth failures
- **scripts/refresh-openclaw-docs.sh** — Downloads the current docs index and linked docs from `https://docs.openclaw.ai/llms.txt` into a local cache

## Installation

Copy the skill files into your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/openclaw-admin
mkdir -p ~/.claude/skills/openclaw-admin/scripts
cp SKILL.md ~/.claude/skills/openclaw-admin/SKILL.md
cp local-install.md ~/.claude/skills/openclaw-admin/local-install.md
cp cli-reference.md ~/.claude/skills/openclaw-admin/cli-reference.md
cp config-map.md ~/.claude/skills/openclaw-admin/config-map.md
cp update-failure-patterns.md ~/.claude/skills/openclaw-admin/update-failure-patterns.md
cp scripts/refresh-openclaw-docs.sh ~/.claude/skills/openclaw-admin/scripts/refresh-openclaw-docs.sh
chmod +x ~/.claude/skills/openclaw-admin/scripts/refresh-openclaw-docs.sh
```

On first use, the agent should fill `local-install.md` from read-only discovery commands. Keep user-specific install facts in that file rather than editing `SKILL.md`.

OpenClaw package installs include local docs under the installed package root. The refresh script is a recommended optional live-docs cache for current published docs, local search, and generated artifacts that are not bundled in npm. The cache defaults to `${XDG_CACHE_HOME:-~/.cache}/openclaw-admin/openclaw-docs`.

When publishing or sharing the skill, keep `local-install.md` as the generic template or remove private host details first.

## Skill Layout

```text
openclaw-admin/
|-- SKILL.md
|-- local-install.md
|-- cli-reference.md
|-- config-map.md
|-- update-failure-patterns.md
`-- scripts/
    `-- refresh-openclaw-docs.sh
```

## Tested with

- OpenClaw 2026.4.x and update/debug patterns observed through 2026.5.x
- Claude Code with superpowers plugin

## Attribution

Update failure patterns are adapted from the MIT-licensed `BKF-Gitty/openclaw-update-runbook` project and generalized for non-host-specific OpenClaw installs.
