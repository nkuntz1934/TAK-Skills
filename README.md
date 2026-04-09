# TAK Server Skills

A collection of [Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) for working with TAK Server APIs, including Mission/Data Sync, WebCOP, Federation, Certificate Management, and CoT (Cursor on Target).

## Installing

### Claude Code

Add as an extra marketplace source, then install the plugin:

```
# Add the private marketplace source
# In settings.json, add to "extraKnownMarketplaces":
#   "tak": { "source": { "source": "github", "repo": "nkuntz1934/tak-skills" } }

# Then install
/plugin install tak@tak
```

Or install directly from GitHub:

```
/plugin add github:nkuntz1934/tak-skills
```

### Cursor

Add via **Settings > Rules > Add Rule > Remote Rule (Github)** with `nkuntz1934/tak-skills`.

### Clone / Copy

Clone this repo and copy the skill and command folders into your agent's config:

```bash
git clone git@github.com:nkuntz1934/tak-skills.git
cp -r tak-skills/skills/tak-server ~/.claude/skills/
cp -r tak-skills/commands/* ~/.claude/commands/
```

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
