# openclaw-admin skill

An operational skill for Claude Code (or any AI coding assistant) that helps diagnose, configure, fix, and tune OpenClaw installations.

## What's included

- **SKILL.md** — Main reference: diagnostic ladder, safe config editing, common pitfalls, plugin/ACP/channel docs
- **cli-reference.md** — Complete CLI command catalog
- **config-map.md** — Config key reference with hot-reload vs restart behavior and common patterns

## Installation

Copy the skill files into your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/openclaw-admin
cp SKILL.generic.md ~/.claude/skills/openclaw-admin/SKILL.md
cp cli-reference.generic.md ~/.claude/skills/openclaw-admin/cli-reference.md
cp config-map.generic.md ~/.claude/skills/openclaw-admin/config-map.md
```

Then customize the Rescue Bot and Internal Hooks sections for your setup.

## Tested with

- OpenClaw 2026.4.x
- Claude Code with superpowers plugin
