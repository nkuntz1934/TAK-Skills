# TAK Server Skills

A collection of [Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) for working with TAK Server APIs, including Mission/Data Sync, WebCOP, Federation, Certificate Management, and CoT (Cursor on Target).

## Installing

### Claude Code

Install using the plugin marketplace:

```
/plugin marketplace add nkuntz1934/tak-skills
/plugin install tak@tak
```

### Clone / Copy

Clone this repo and copy the skill folders into the appropriate directory for your agent:

| Agent | Skill Directory | Docs |
|-------|-----------------|------|
| Claude Code | `~/.claude/skills/` | [docs](https://code.claude.com/docs/en/skills) |
| Cursor | `~/.cursor/skills/` | [docs](https://cursor.com/docs/context/skills) |

## Commands

Commands are user-invocable slash commands that you explicitly call.

| Command | Description |
|---------|-------------|
| `/tak:tak_docs` | Load the full TAK Server API reference for the current conversation |

## Skills

Skills are contextual and auto-loaded based on your conversation.

| Skill | Useful for |
|-------|------------|
| tak-server | Comprehensive TAK Server API skill covering Missions, Data Sync, WebCOP, Federation, Certificates, CoT, QoS, Retention, and server configuration |

## Source Documentation

The skills in this repo are derived from the official TAK Server API documentation:

- **OpenAPI 3.1.0 Spec** (370 pages) - Full endpoint and schema definitions
- **TAK Server Mission API Definition** - Data Sync / Mission API guide with examples
- **TAK Server WebCOP API Definition** - Browser-based WebSocket/STOMP messaging API
