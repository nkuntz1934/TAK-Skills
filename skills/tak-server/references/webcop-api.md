# TAK Server WebCOP API

The WebCOP API enables ATAK-like functionality in a web browser using REST APIs and WebSocket/STOMP messaging for real-time SA (Situational Awareness), chat, and mission change notifications.

## Establishing a WebSocket / STOMP Feed

### Step 1: Get a topic

Request a topic UID for your user. Authentication is inferred from the client certificate.

```bash
curl 'https://<server>:8443/Marti/api/cop/topic?callsign=bob&team=orange&role=HQ&takv=WebCOP:0.1'
```

Response:
```json
{"version": "1.0.0", "type": "Topic", "data": "13876e57d2f24273aaeb2482c12eb5ca"}
```

Optional parameters: `callsign`, `team`, `role`, `takv`, `clientSeed`

The `clientSeed` parameter assigns a unique random value to the topic. If provided, multiple sessions for the same user credential will get different topics. If omitted, sessions with the same credential share the same topic.

### Step 2: Connect WebSocket and subscribe via STOMP

```javascript
var socket = new SockJS('/Marti/api/cop');
stompClient = Stomp.over(socket);

var getTopicAndSubscribe = function() {
  $.get("/Marti/api/cop/topic", function(data) {
    var topic = data.data;
    stompClient.connect({}, function(frame) {
      stompClient.subscribe('/topic/' + topic, processMessage);
    });
  });
}
```

STOMP uses text-based frames with properties: `command` (String), `headers` (JS object), `body` (String).

### Step 3: Periodically refresh

The WebSocket connection and topic must be periodically refreshed as directed by `REFRESH_TOPIC` control messages. Handle these in `processMessage` by tearing down and recreating the websocket.

## Message Types

### SituationAwarenessMessage

Received when CoT events matching the user's group filter are published. The body is a JSON object with CoT fields.

| Field | Type | Description |
|-------|------|-------------|
| messageType | String | `"SituationAwarenessMessage"` |
| uid | String | Device/entity unique ID |
| type | String | CoT type code (e.g., `"a-f-G-U-C"`) |
| lat | Double | Latitude |
| lon | Double | Longitude |
| hae | Double | Height above ellipsoid (altitude) |
| start | CoT Timestamp | Event start time |
| time | CoT Timestamp | Event time |
| stale | CoT Timestamp | When the event goes stale |
| callsign | String | Human-readable name |
| color | String | One of ATAK's 16 colors |
| role | String | `"Team Member"`, `"Team Lead"`, or `"HQ"` |
| iconsetPath | String | Icon path (e.g., `"34ae1613.../Buildings/office.png"`) |

Raw STOMP frame example:
```
["MESSAGE\ndestination:/topic/13876e57d2f24273aaeb2482c12eb5ca\ncontent-type:application/json;charset=UTF-8\n...\n\n{\"messageType\":\"SituationAwarenessMessage\",\"uid\":\"142db25e-1be5-36cb-8b5b-dbd1183f2bb2\",\"type\":\"a-f-G-U-C\",\"lat\":42.3803274,\"lon\":-71.13891009999999,...}\u0000"]
```

### Sending Your Own SA

Send position via STOMP:

```javascript
geolocation.on('change:position', function() {
  var coordinates = geolocation.getPosition();
  if (coordinates) {
    var wgsCoord = ol.proj.transform(coordinates, 'EPSG:900913', 'EPSG:4326');
    if (myuid != undefined) {
      stompClient.send("/cop/cop", {}, JSON.stringify({
        'lat': wgsCoord[1],
        'lon': wgsCoord[0],
        'type': 'a-f-G-U-C',
        'uid': myuid,
        'callsign': mycallsign
      }));
    }
  }
});
```

Currently supported CoT fields for self-marker: `uid`, `callsign`, `type`, `lat`, `lon`.

### ChatMessage

Chat messages use the same WebSocket/STOMP channel. STOMP frame type is `MESSAGE` (server→browser) or `SEND` (browser→server).

| Field | Type | Description |
|-------|------|-------------|
| messageType | String | `"ChatMessage"` |
| from | String | Client UID of sender |
| addresses | String[] | Destinations in format `<type>:<identifier>` |
| lat | Number | Sender latitude |
| lon | Number | Sender longitude |
| hae | Number | Sender altitude |
| body | String | Message text |
| timestamp | Number | When sent |

Address types: `uid`, `mission`, `group`

Messages are routed based on group membership. TAK Server tracks group membership based on the WebCOP user's identity. Messages can be addressed to ATAK client UIDs, missions, TAK Server groups, or websocket topics interchangeably.

### MissionChange Notification

Received when a subscribed mission changes.

| Field | Type | Description |
|-------|------|-------------|
| messageType | String | `"MissionChange"` |
| missionName | String | Name of the changed mission |
| cotType | String | `"t-x-m-c"` (mission change) or `"t-x-m-c-l"` (log change) |
| time | CoT Timestamp | When the change occurred |

Triggers: Create Mission, Delete Mission, Add Content (by hash or uid), Create/Update/Delete Mission Log Entry.

## Icon Resolution

Display icons should follow ATAK conventions:

```
# By color
GET /Marti/api/iconurl?cotType=a-f-G-U-C-I&groupName=Purple

# By iconset path
GET /Marti/api/iconurl?cotType=a-f-G-U-C-I&iconsetpath=34ae1613-9645-4222-a9d2-e5f243dea2865/Buildings/office.png

# Fallback to CoT type (returns MIL-STD 2525B icon)
GET /Marti/api/iconurl?cotType=a-f-G-U-C-I
```

Resolution order:
1. If SA message has `color` → use `iconurl` with `groupName`
2. If SA message has `iconsetPath` → use `iconurl` with `iconsetpath`
3. Else → use `iconurl` with just `cotType` (falls back to 2525B)

The `cotType` parameter is required; `groupName` and `iconsetpath` are optional.

## Contacts and Groups

```bash
# Get all contacts
curl "http://<user>:<pass>@localhost:8080/Marti/api/contacts/all"
curl "http://<user>:<pass>@localhost:8080/Marti/api/contacts/all?sortBy=UID&direction=DESCENDING"

# Get all groups
curl "http://<user>:<pass>@localhost:8080/Marti/api/groups/all"
```

Contact response:
```json
[
  {"notes": "Anonymous_329994173", "callsign": "anon1", "uid": "anon1"},
  {"notes": "", "callsign": "Anonymous_COTRMI", "uid": "Anonymous_COTRMI"}
]
```

Group response (standard API envelope with group objects containing `name`, `direction`, `created`, `type`, `bitpos`).

## Properties API

Server-side per-UID key-value storage for user settings and preferences:

```bash
# Store a key-value pair
PUT https://gov.atakserver.com:8443/Marti/api/properties/alice
Body: {"chatroom": "HQ"}

# Get all properties for a UID
GET https://gov.atakserver.com:8443/Marti/api/properties/alice/all

# Get values for a specific key
GET https://gov.atakserver.com:8443/Marti/api/properties/alice/team

# Delete a key
DELETE https://gov.atakserver.com:8443/Marti/api/properties/alice/team

# Delete all properties for a UID
DELETE https://gov.atakserver.com:8443/Marti/api/properties/alice/all
```
