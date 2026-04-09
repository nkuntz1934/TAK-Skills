---
name: tak-server
description: "TAK Server API reference covering Missions, Data Sync, WebCOP, Federation, Certificates, CoT (Cursor on Target), and server administration. Load this skill whenever the user mentions TAK, ATAK, TAK Server, CoT events, missions, data sync, WebCOP, or is building any integration that talks to a TAK Server -- even if they just say 'connect to the server' or 'send position data' in a TAK project context. Also load when you see TAK-related paths like /Marti/api/ or references to callsigns, UIDs, or mission packages."
---

# TAK Server API

TAK (Team Awareness Kit) Server provides situational awareness through real-time position tracking (CoT), mission-based data sharing, and federated server connectivity. The API is a REST interface (OpenAPI 3.1.0) served over HTTPS on port 8446 (TLS/mTLS) or HTTP on port 8080.

## When to consult references

This skill is organized into focused reference files. Read the one that matches what the user needs:

| Reference | When to read |
|-----------|-------------|
| `references/endpoints.md` | Looking up any specific API endpoint, path, method, or parameters |
| `references/mission-api.md` | Working with missions, data sync, subscriptions, content, or mission packages |
| `references/webcop-api.md` | Building browser-based SA, WebSocket/STOMP messaging, chat, or COP views |
| `references/schemas.md` | Configuration schemas, response formats, or data types |

## Core Concepts

### Authentication
TAK Server supports two auth methods:
- **Client certificates (mTLS)** on port 8446 -- the primary method. The server infers identity from the client cert.
- **Username:password basic auth** on port 8080 -- used for development and non-TLS setups.

### API Response Format
All JSON responses follow a standard envelope:

```json
// Success
{"version": "1.0.0", "type": "<ResourceType>", "data": [...]}

// Error (404, 400, etc.)
{"status": "NOT_FOUND", "code": 1, "message": "Human-readable error"}
```

- **Timestamps** are ISO-8601 with milliseconds: `2016-04-22T15:26:17.260Z`
- **Content-Type** is `application/json;charset=UTF-8` unless returning binary files
- REST verbs follow HTTP semantics: GET=read, PUT=create/update, DELETE=delete
- Many PUT operations are idempotent (e.g., creating a mission that already exists succeeds)

### CoT (Cursor on Target)
CoT is the XML-based messaging format used across all TAK products. Key fields:
- **uid** -- unique identifier for a device/entity (e.g., `ANDROID-48:5A:3F:52:29:78`)
- **type** -- hierarchical code describing the entity (e.g., `a-f-G-U-C` = friendly ground unit combat)
- **callsign** -- human-readable name
- **lat/lon/hae** -- WGS84 position and altitude
- **time/start/stale** -- event timestamps controlling display lifecycle

### Missions
Missions are the primary collaboration mechanism. They contain:
- **CoT tracks** (by UID) -- real-time position data
- **Enterprise sync resources** (by hash) -- files like images, KML, GRGs
- **Mission logs** -- free-form text entries ("Recce Logs")

Content is referenced by deterministic hash of binary content. Clients subscribe to missions and receive change notifications. Mission roles control access: `MISSION_OWNER`, `MISSION_SUBSCRIBER`, `MISSION_READONLY_SUBSCRIBER`.

### API Groups Overview

The TAK Server API is organized into these groups:

**Core Data:**
- **mission-api** -- CRUD for missions, content, subscriptions, invitations, layers, keywords, feeds
- **cot-api** -- Query CoT events by time/bounding box
- **metadata-api** -- Set metadata, keywords, and expiration on sync resources
- **file-manager-api** -- File metadata management

**Real-time / Browser:**
- **cop-view-api** -- COP mission listing and hierarchy
- **contacts-api** -- Connected TAK clients and their callsigns
- **contact-manager-api** -- Client endpoint discovery

**Administration:**
- **security-authentication-api** -- Security and auth configuration
- **cert-manager-admin-api** -- Certificate lifecycle (issue, revoke, download, list)
- **file-user-account-management-api** -- User CRUD, groups, passwords
- **config-api** -- Core server configuration
- **submission-api** -- Input/output management, data feeds, store-forward chat
- **retention-api** -- Data retention policies and schedules

**Collaboration:**
- **federation-api** -- Inter-server federation connections and config
- **federation-config-api** -- Federation configuration
- **ex-check-api** -- Execution checklists with tasks and mission references
- **map-layers-api** -- Map layer CRUD
- **data-feed-api** -- Predicate-based data feeds

**Other:**
- **subscription-api** -- Client subscriptions, filters, active groups
- **properties-api** -- Per-UID key-value storage
- **plugin-data-api** -- Plugin data submission
- **qo-s-api** -- Quality of Service rate limiting
- **video-connection-manager-v-2** -- Video feed connections
- **iconset-icon-api** -- Icon resolution by CoT type, color, or iconset
- **token-api** -- Token revocation
- **registration-api** -- User registration and invites
- **locate-api** -- Geospatial locate queries
- **ci-trap-report-api** -- CI trap reports
- **vbm-configuration-api** -- VBM (Virtual Battle Map) config
- **profile-admin-api** -- Device profile management
- **xmpp-api** -- XMPP file transfer
- **o-auth-api** -- OAuth logout
