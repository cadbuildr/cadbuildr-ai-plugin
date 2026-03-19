# CADBuildr AI Plugin

This repository provides CADBuildr skills and MCP configuration for customer workflows in Claude Code, Cursor, Codex, and Mistral Vibe.

## Prerequisites

Set your CADbuildr API key before starting your AI tool:

```bash
export CADBUILDR_API_KEY="your_api_key"
```

To obtain a key go to `hub.cadbuildr.com/settings`

## Claude Code setup

### Install from CADbuildr marketplace (recommended)

Run Claude Code from your project directory:

```bash
claude
```

Then run these commands **inside the Claude chat session** (not in your shell):

```text
/plugin marketplace add cadbuildr/cadbuildr-ai-plugin
/plugin install cadbuildr@cadbuildr-ai-plugin
/reload-plugins
```

What each command does:

- `/plugin marketplace add ...` registers the CADbuildr marketplace (usually one-time per machine/scope)
- `/plugin install ...` installs the CADbuildr plugin from that marketplace
- `/reload-plugins` reloads installed plugins in the current Claude session

### Local development fallback (`--plugin-dir`)

Run Claude from your project with this plugin directory:

```bash
claude --plugin-dir /absolute/path/to/cadbuildr-ai-plugin
```

Then in the Claude session:

```text
/reload-plugins
```

## Cursor setup

Use this repository as your plugin source for Cursor. Cursor reads:

- `.cursor-plugin/plugin.json`
- `skills/*/SKILL.md`
- `.mcp.json`

## Codex setup

Codex discovers repository skills from `.agents/skills` (from your current working directory up to the repository root).

This repository includes Codex wrappers at:

- `.agents/skills/*/SKILL.md`

Each wrapper points to the canonical implementation in:

- `skills/*/SKILL.md`

### Run with this repo

From this repository root:

```bash
codex
```

Then ask Codex for CADBuildr workflows (for example, foundation setup, render/export, or broker/viewer setup).

### MCP server for Codex

Codex MCP configuration depends on your local Codex client setup. Reuse the same CADBuildr server values from `.mcp.json`:

- name: `cadbuildr`
- url: `https://mcp.cadbuildr.com/mcp/`
- header: `x-api-key: ${CADBUILDR_API_KEY}`

## Mistral Vibe setup

Run Vibe from this repository root:

```bash
vibe --workdir .
```

Vibe discovers skills through `.vibe/config.toml`.

## Repository contents

- `.claude-plugin/plugin.json` - Claude Code plugin metadata
- `.claude-plugin/marketplace.json` - Claude Code marketplace catalog
- `.cursor-plugin/plugin.json` - Cursor plugin metadata
- `.agents/skills/*/SKILL.md` - Codex skill discovery wrappers
- `.mcp.json` - CADbuildr MCP server configuration
- `.vibe/config.toml` - Mistral Vibe MCP and skills configuration
- `skills/*/SKILL.md` - CADbuildr workflow skills
