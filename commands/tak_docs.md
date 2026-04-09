---
description: Load the full TAK Server API reference for the current conversation
allowed-tools: [Read, Glob, Grep, Bash, Write, Edit, WebFetch]
---

# TAK Server API Reference

This command loads the TAK Server API reference into the current conversation context.

## Instructions

When this command is invoked:

1. Read the skill file at `skills/tak-server/SKILL.md` for the full API endpoint reference
2. Reference `skills/tak-server/references/mission-api.md` for Mission/Data Sync API details with curl examples
3. Reference `skills/tak-server/references/webcop-api.md` for WebCOP WebSocket/STOMP messaging
4. Reference `skills/tak-server/references/schemas.md` for configuration schemas and response types

## Capabilities

- Full TAK Server REST API endpoint reference (OpenAPI 3.1.0)
- Mission/Data Sync API with CRUD operations, subscriptions, and content management
- WebCOP browser API with WebSocket/STOMP for SA, Chat, and MissionChange messages
- Federation, certificate management, retention, QoS, and server config APIs
- CoT (Cursor on Target) event handling and icon resolution
