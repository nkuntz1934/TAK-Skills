# TAK Server Skills

> **Work in Progress** -- This skill is under active development. If you find anything inaccurate, please submit a PR.

A collection of [Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) for working with TAK Server APIs, including Mission/Data Sync, WebCOP, Federation, Certificate Management, and CoT (Cursor on Target).

## Installing

### Claude Code (recommended)

Add as an extra marketplace source in your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "tak": {
      "source": {
        "source": "github",
        "repo": "nkuntz1934/tak-skills"
      }
    }
  }
}
```

Then install the plugin from inside Claude Code:

```
/plugin install tak@tak
/reload-plugins
```

Alternatively, load the skill directory directly for a single session:

```bash
claude --plugin-dir /path/to/tak-skills
```

### Cursor

Add via **Settings > Rules > Add Rule > Remote Rule (Github)** with `nkuntz1934/tak-skills`.

### Manual Install (any agent)

Clone this repo and copy the skill and command folders into your agent's config directory:

```bash
git clone git@github.com:nkuntz1934/tak-skills.git
```

Then copy into the appropriate location:

| Agent | Copy skills to | Copy commands to |
|-------|---------------|-----------------|
| Claude Code | `~/.claude/skills/tak-server/` | `~/.claude/commands/` |
| Cursor | `~/.cursor/skills/tak-server/` | n/a |

Example for Claude Code:
```bash
cp -r tak-skills/skills/tak-server ~/.claude/skills/
cp tak-skills/commands/tak_docs.md ~/.claude/commands/
```

## Commands

Commands are user-invocable slash commands.

| Command | Description |
|---------|-------------|
| `/tak:tak_docs` | Load the full TAK Server API reference for the current conversation |

## Skills

Skills are contextual and auto-loaded when your conversation matches TAK-related topics.

| Skill | Useful for |
|-------|------------|
| tak-server | Comprehensive TAK Server API skill covering 45+ API groups: Missions, Data Sync, WebCOP, Federation, Certificates, CoT, QoS, Retention, Plugins, Profiles, and server administration |

## What's Included

The skill is organized with progressive disclosure -- a lean overview that points to detailed reference files:

```
skills/tak-server/
├── SKILL.md                          # Overview, core concepts, API group index
└── references/
    ├── endpoints.md                  # Every endpoint from the OpenAPI spec
    ├── mission-api.md                # Mission/Data Sync guide with curl examples
    ├── webcop-api.md                 # WebCOP WebSocket/STOMP messaging
    └── schemas.md                    # Configuration schemas and data models
```

## Source Documentation

Derived from the official TAK Server API documentation:

- **OpenAPI 3.1.0 Spec** (370 pages) -- Full endpoint and schema definitions
- **TAK Server Mission API Definition** -- Data Sync / Mission API guide with examples
- **TAK Server WebCOP API Definition** -- Browser-based WebSocket/STOMP messaging API
