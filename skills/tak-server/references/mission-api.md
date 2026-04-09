# TAK Server Mission API (Data Sync)

Detailed guide for the Mission/Data Sync API with curl examples and response formats. This is the most commonly used part of the TAK Server API.

## Overview

The Data Sync / Mission API lets clients share collections of information as **missions**. Three types of content can be associated with a mission:

1. **CoT tracks** -- referenced by UID, the server provides the latest CoT event for each UID
2. **Enterprise sync resources** -- files (GRGs, images, KML) referenced by deterministic hash
3. **Mission logs** -- free-form text entries (also called "Recce Logs")

Clients subscribe to missions and receive change notifications. The API is idempotent where possible -- creating a mission that already exists succeeds.

## curl Examples

All examples use basic auth. For mTLS, replace `http://<user>:<pass>@localhost:8080` with `https://<server>:8446` and add `--cert`/`--key` flags.

### Get all missions
```bash
curl "http://<user>:<pass>@localhost:8080/Marti/api/missions"
```

### Get a single mission
```bash
curl "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh"
```

### Create a mission
```bash
curl -X PUT "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh?creatorUid=bob"
```
Response (201 Created):
```json
{
  "version": "1.0.0",
  "type": "Mission",
  "data": [{
    "name": "pittsburgh",
    "keywords": [],
    "creatorUid": "bob",
    "createTime": "2016-05-03T15:22:44.107Z",
    "uids": [],
    "contents": []
  }]
}
```

### Delete a mission
```bash
curl -X DELETE "http://<user>:<pass>@localhost:8080/Marti/api/missions/xyz"
```

### Add content to a mission

Content can be referenced by hash (files) and/or uid (CoT tracks):

```bash
# By file hashes
curl -X PUT "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh/contents" \
  -H "Content-Type: application/json" \
  -d '{"hashes": ["12fg", "789n"]}'

# By CoT UIDs
curl -X PUT "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh/contents" \
  -H "Content-Type: application/json" \
  -d '{"uids": ["asdf", "zxv"]}'

# Both
curl -X PUT "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh/contents" \
  -H "Content-Type: application/json" \
  -d '{"hashes": ["12fg", "789n"], "uids": ["asdf", "zxv"]}'
```

### Remove content from a mission

Remove by hash or uid (one at a time):
```bash
curl -v -X DELETE "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh/contents?hash=789n"
curl -v -X DELETE "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh/contents?uid=xyz"
```

### Get mission CoT events (XML)
```bash
curl "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh/cot"
```
Returns XML with `<event>` elements containing `<point>`, `<detail>`, `<contact>`, `<__group>`, etc.

### Get mission changes
```bash
curl "http://<user>:<pass>@localhost:8080/Marti/api/missions/pittsburgh/changes"
```
Query params:
- `secago` -- seconds in the past (from now)
- `start` / `end` -- time interval (start takes precedence over secago)

Response contains change records with types like `CREATE_MISSION`, `ADD_CONTENT`, etc.

## Mission Response Format

A full mission object includes:

```json
{
  "name": "train",
  "keywords": [],
  "creatorUid": "bob",
  "createTime": "2016-04-22T15:26:17.260Z",
  "uids": [
    {
      "data": "ANDROID-48:5A:3F:52:29:78",
      "timestamp": "2016-05-02T14:11:00.888Z",
      "creatorUid": "joe1234"
    }
  ],
  "contents": [
    {
      "data": {
        "keywords": [],
        "mimeType": "application/x-zip-compressed",
        "name": "Wide_photo.kmz",
        "submissionTime": "2016-04-22T15:31:02.829Z",
        "submitter": "admin",
        "uid": "9698hhsg",
        "creatorUid": "yyy666",
        "hash": "78cd540c8e352e6ff416fc1ec48de602c20a6274d1c1201409a5fbb107f23f8f",
        "size": 55981
      },
      "timestamp": "2016-04-22T15:31:47.800Z",
      "creatorUid": "joe1234"
    }
  ]
}
```

## Mission Change Types

Change records in the changelog use these types:
- `CREATE_MISSION` -- mission was created
- `ADD_CONTENT` -- file (by hash) or CoT track (by UID) was added
- (content removal, log entries, etc. follow similar patterns)

## Mission Roles

When creating subscriptions or setting roles:
- `MISSION_OWNER` -- full control
- `MISSION_SUBSCRIBER` -- read/write
- `MISSION_READONLY_SUBSCRIBER` -- read only

## Invite Types

When inviting users to a mission, the `type` path parameter specifies how to identify the invitee:
- `clientUid` -- by device UID
- `callsign` -- by callsign
- `userName` -- by username
- `group` -- by group name
- `team` -- by team name
