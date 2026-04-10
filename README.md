# TAK Server Skills

> **Work in Progress** -- This skill is under active development. If you find anything inaccurate, please submit a PR.

A collection of [Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) for working with TAK Server APIs, including Mission/Data Sync, WebCOP, Federation, Certificate Management, and CoT (Cursor on Target).

## Installing

### Claude Code

Clone this repo and copy the skill and command into your Claude Code config directory:

```bash
git clone https://github.com/nkuntz1934/TAK-Skills.git
mkdir -p ~/.claude/skills
cp -r tak-skills/skills/tak-server ~/.claude/skills/
cp tak-skills/commands/tak_docs.md ~/.claude/commands/
```

The skill will auto-load contextually when working on TAK-related topics. Use `/tak_docs` to load the full API reference on demand.

### Cursor

Clone this repo and copy the skill into your Cursor config directory:

```bash
git clone https://github.com/nkuntz1934/TAK-Skills.git
mkdir -p ~/.cursor/skills
cp -r tak-skills/skills/tak-server ~/.cursor/skills/
```

## Commands

Commands are user-invocable slash commands.

| Command | Description |
|---------|-------------|
| `/tak_docs` | Load the full TAK Server API reference for the current conversation |

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
