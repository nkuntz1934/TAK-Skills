# TAK Server Connections & Authentication

Guide to connecting to TAK Server, authentication methods, CoT publishing, and certificate management. Derived from the OpenAPI spec schemas and Mission API documentation.

## Authentication Types

TAK Server supports four authentication methods, configured per-input via the `Input` schema's `auth` field:

| Auth Type | Value | Description |
|-----------|-------|-------------|
| **Client Certificate (mTLS)** | `X_509` | Primary method. Server infers identity from client cert. Used on TLS-enabled ports (typically 8443, 8446). |
| **File-based Auth** | `FILE` | Username/password basic auth. Users managed via file-user-account-management-api. Used on HTTP port (typically 8080). |
| **LDAP** | `LDAP` | LDAP directory authentication. Configured via authentication-config-api. |
| **Anonymous** | `ANONYMOUS` | No authentication required. Connections placed in the `__ANON__` group. Enable with `anongroup: true` on the input. |

## Port Reference

Ports observed in the official API documentation:

| Port | Protocol | Auth | Used for |
|------|----------|------|----------|
| 8080 | HTTP | Basic auth (FILE) | REST API (dev/non-TLS), Mission API curl examples |
| 8443 | HTTPS | Client cert (X_509) | WebCOP API, Properties API, operational use |
| 8446 | HTTPS | Client cert (X_509) | Admin API / OpenAPI spec endpoint |
| 4242 | TCP | Varies | CoT streaming (seen in contact endpoint fields as `endpoint="10.0.1.6:4242:tcp"`) |

Additional ports are configurable via the `Input` schema's `port` field when creating inputs through the submission-api.

## Input Configuration

Inputs define how TAK Server accepts connections. Managed via the submission-api:

- `POST /Marti/api/inputs` — Create a new input
- `GET /Marti/api/inputs` — List all inputs with metrics
- `PUT /Marti/api/inputs/{id}` — Modify an input
- `DELETE /Marti/api/inputs/{name}` — Delete an input

### Input Schema

Key fields from the `Input` schema:

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Input name (XML name: `_name`) |
| `protocol` | string | Connection protocol |
| `port` | int32 | Listening port |
| `auth` | enum | `LDAP`, `FILE`, `ANONYMOUS`, `X_509` |
| `authRequired` | boolean | Whether authentication is required |
| `group` | string | Default group for connections |
| `iface` | string | Network interface to bind |
| `archive` | boolean | Whether to archive received data |
| `anongroup` | boolean | Place unauthenticated users in `__ANON__` group |
| `archiveOnly` | boolean | Archive only, don't route messages |
| `federateOnly` | boolean | Only accept federated connections |
| `coreVersion` | int32 | Protocol version |
| `filtergroup` | string[] | Group filter list |
| `filter` | Filter | Content filter configuration |
| `maxMessageReadSizeBytes` | int32 | Max message size |
| `binaryPayloadWebsocketOnly` | boolean | Restrict binary payloads to WebSocket |

### InputMetric Schema

Monitoring fields returned by `GET /Marti/api/inputs`:

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Input identifier |
| `input` | Input | Input configuration |
| `readsReceived` | int64 | Number of reads received |
| `messagesReceived` | int64 | Messages received count |
| `numClients` | int32 | Currently connected clients |
| `bytesReceived` | int64 | Total bytes received |

## Certificate Management

### Generating Client Certificates

Use the cert-manager-api to generate keystores for client connections:

```
GET /Marti/api/tls/makeClientKeyStore?cn=<commonName>&clientUid=<uid>&password=atakatak
```

Parameters:
- `cn` (optional) — Common name for the certificate
- `clientUid` (optional) — Client UID to associate
- `password` (default: `atakatak`) — Keystore password

Returns: Binary keystore file (PKCS12)

### Signing Client Certificates

```
POST /Marti/api/tls/signClient
POST /Marti/api/tls/signClient/v2
```

Parameters: `clientUid`, `version`. Body: CSR string.

### TLS Configuration

```
GET /Marti/api/tls/config
```

Returns the server's TLS configuration string.

### Certificate Administration

Full certificate lifecycle via cert-manager-admin-api:

| Operation | Endpoint |
|-----------|----------|
| List all certs | `GET /Marti/api/certadmin/cert` (query: `username`) |
| Get cert by hash | `GET /Marti/api/certadmin/cert/{hash}` |
| Download cert | `GET /Marti/api/certadmin/cert/{hash}/download` |
| Revoke cert | `DELETE /Marti/api/certadmin/cert/{hash}` |
| List active | `GET /Marti/api/certadmin/cert/active` |
| List expired | `GET /Marti/api/certadmin/cert/expired` |
| List revoked | `GET /Marti/api/certadmin/cert/revoked` |
| Bulk download | `GET /Marti/api/certadmin/cert/download/{ids}` |
| Bulk revoke | `DELETE /Marti/api/certadmin/cert/revoke/{ids}` |
| Bulk delete | `DELETE /Marti/api/certadmin/cert/delete/{ids}` |

### SecurityConfigInfo Schema

Server security configuration (via `GET /Marti/api/security/config`):

| Field | Type | Description |
|-------|------|-------------|
| `keystoreFile` | string | Path to server keystore |
| `truststoreFile` | string | Path to truststore |
| `keystorePass` | string | Keystore password |
| `truststorePass` | string | Truststore password |
| `tlsVersion` | string | TLS version |
| `x509Groups` | boolean | Use X.509 cert attributes for group assignment |
| `x509addAnon` | boolean | Also add X.509 users to anonymous group |
| `enableEnrollment` | boolean | Allow certificate enrollment |
| `caType` | string | CA type |
| `signingKeystoreFile` | string | Signing CA keystore |
| `signingKeystorePass` | string | Signing CA keystore password |
| `validityDays` | int32 | Certificate validity period in days |
| `mscaUserName` | string | Microsoft CA username (if applicable) |
| `mscaTruststore` | string | Microsoft CA truststore |
| `mscaTemplateName` | string | Microsoft CA template |

## CoT (Cursor on Target) XML Format

CoT is the XML messaging format used across all TAK products. Full example from the Mission API documentation:

```xml
<event version='2.0' uid='ANDROID-48:5A:3F:52:29:78' type='a-f-G-U'
  time='2016-05-02T14:11:29Z' start='2016-05-02T14:11:29Z'
  stale='2016-05-02T14:12:13Z' how='h-e'>
    <point lat='38.87925058057263' lon='-77.10781575234559'
           hae='56.5731172456302' ce='9999999.0' le='9999999.0' />
    <detail>
        <contact phone="15712128188" endpoint="10.0.1.6:4242:tcp"
                 callsign="The Dude"/>
        <uid Droid="The Dude"/>
        <__group name="Rad Sensor" role="Team Lead"/>
        <status battery="59"/>
        <track speed="0.0" course="278.41118524401173"/>
        <precisionlocation geopointsrc="User" altsrc="DTED0"/>
        <_flow_tags_ demo1="2016-05-02T10:11:29Z"/>
    </detail>
</event>
```

### Event Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `version` | Yes | CoT version (always `2.0`) |
| `uid` | Yes | Unique identifier (e.g. `ANDROID-48:5A:3F:52:29:78`) |
| `type` | Yes | Hierarchical type code (e.g. `a-f-G-U-C` = friendly ground unit combat) |
| `time` | Yes | Event timestamp (ISO-8601) |
| `start` | Yes | Event start time (ISO-8601) |
| `stale` | Yes | Stale time — event expires after this (ISO-8601) |
| `how` | No | How the position was derived (e.g. `h-e` = human estimated, `m-g` = GPS) |

### Point Element

| Attribute | Required | Description |
|-----------|----------|-------------|
| `lat` | Yes | Latitude (WGS84 decimal degrees) |
| `lon` | Yes | Longitude (WGS84 decimal degrees) |
| `hae` | Yes | Height above ellipsoid (meters) |
| `ce` | Yes | Circular error (meters, 9999999.0 = unknown) |
| `le` | Yes | Linear error (meters, 9999999.0 = unknown) |

### Detail Sub-Elements

| Element | Description |
|---------|-------------|
| `<contact>` | Callsign, phone, endpoint (`address:port:protocol`) |
| `<uid>` | Droid name (human-readable device name) |
| `<__group>` | Team name and role (`Team Member`, `Team Lead`, `HQ`) |
| `<status>` | Device status (battery level) |
| `<track>` | Movement (speed in m/s, course in degrees) |
| `<precisionlocation>` | Position source (`GPS`, `User`, `???`) and altitude source |
| `<_flow_tags_>` | Routing metadata with timestamps |

### CoT Type Codes

The `type` field follows a hierarchical scheme: `a-{affiliation}-{battle dimension}-{function}`

Common affiliations: `f` (friendly), `h` (hostile), `u` (unknown), `n` (neutral)
Common battle dimensions: `G` (ground), `A` (air), `S` (sea)

Examples:
- `a-f-G-U-C` — friendly ground unit, combat
- `a-f-G-U-C-I` — friendly ground unit, combat, infantry
- `a-h-A` — hostile air
- `b-m-p-s-m` — bits, mission package, sensor, map

## Publishing CoT via WebSocket/STOMP

The WebCOP API provides browser-based CoT publishing via WebSocket:

1. Connect via SockJS: `new SockJS('/Marti/api/cop')`
2. Initialize STOMP: `Stomp.over(socket)`
3. Get topic: `GET /Marti/api/cop/topic?callsign=X&team=Y&role=Z&takv=WebCOP:0.1`
4. Subscribe: `stompClient.subscribe('/topic/' + topicId, processMessage)`
5. Send SA: `stompClient.send("/cop/cop", {}, JSON.stringify({lat, lon, type, uid, callsign}))`

See `references/webcop-api.md` for full WebSocket/STOMP documentation.

## Profile Enrollment

Device profile enrollment for first-time connections:

```
GET /Marti/api/tls/profile/enrollment?clientUid=<uid>
```

Returns binary profile data. Used by ATAK during initial server connection to receive configuration, certificates, and preferences.

Additional profile endpoints:
- `GET /Marti/api/device/profile/tool/{toolName}` — Get profile by tool
- `GET /Marti/api/device/profile/connection` — Get connection profile
- `GET /Marti/api/device/profile/{name}/missionpackage` — Get profile as mission package

## OAuth / Token Authentication

For OAuth-enabled TAK Server deployments:

| Endpoint | Description |
|----------|-------------|
| `GET /login/auth` | Initiate OAuth auth request |
| `GET /login/authserver` | Get auth server name |
| `GET /token/access` | Get OAuth access token |
| `GET /login/.well-known/openid-configuration` | OpenID discovery |
| `GET /logout` or `POST /logout` | Logout |

The OAuth schema includes: `readOnlyGroup`, `readGroupSuffix`, `writeGroupSuffix`, `groupsClaim`, `usernameClaim`, `scopeClaim`, `webtakScope`, `groupprefix`.
