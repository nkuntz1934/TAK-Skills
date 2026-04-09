# TAK Server Mission API Definition (Data Sync)

## Overview

The Data Sync / Mission API provides the capability in TAK Server for clients, such as ATAK, to share collections of information which are tracked as part of missions. Once a mission has been created by a client, it immediately becomes available to other clients. There are three types of content that can be associated with a mission:

1. **CoT tracks** -- referenced by their UID, so that any information that can be represented in a mission. Once CoT UIDs have been added to a mission, the server provides the latest CoT event associated with each UID.
2. **Enterprise sync resources** -- files (GRGs, images, KML) uploaded via the Enterprise Sync API. Any type of file useful from a client perspective can be added to a mission. These resources are referenced by calculating a deterministic hash for each file, based on the binary contents of the file. The file contents of the mission can be searched by parameters including time interval and geographic location.
3. **Mission Logs** -- free-form text entries (also called "Recce Logs") that can be viewed individually or as a complete log. CoT UIDs and file resources can also be associated with individual log entries. Log entries may also be updated and deleted.

Each client can always maintain a consistent local view of missions and their contents, conserving resources such as local storage and network bandwidth.

Tracks and files can also be removed from the mission. Every change to the state of a mission, including the creation or deletion of the mission itself, is tracked by TAK Server. Based on this information, the server can generate a change log detailing each change to the mission over time. This log can be filtered based on an arbitrary time interval.

Clients may subscribe to one or more missions. Once a client has subscribed to a mission, the server will send it a CoT message informing the client of each change. Based on this notification, the client can then request a change log for an appropriate time range.

Missions and related information are made available to clients via a REST (Representational State Transfer) API. This approach models the mission information as a hierarchy of resources. For example, the "mission" resource contains the "content" sub-resource. API information is encoded as JSON (JavaScript Object Notation) objects. The REST approach applies the standard HTTP verbs GET, POST, PUT and DELETE to mission resources. These verbs correspond, respectively, to the operations "read", "create", "update" and "delete" (CRUD). Request and response headers are also used appropriately, in accordance with the HTTP 1.1 standard. REST operations follow best practices with regard to safety and idempotency. For example, multiple concurrent requests to create a mission of the same name will not be afflicted by concurrency problems such as race conditions or deadlock.

## API Response Format - Success

Successful API responses are encoded as JSON, and follow a standard format. The response is a JSON object with these properties:

- **version**: API version
- **type**: Type of data contained in the data array
- **data**: A JSON array of objects, of the indicated type. If the REST request result is empty, the array will be empty (but not null). In the case of a single response, there will be one element in the array. For a response containing multiple items, these items will be in the array.

```json
{
  "version": "1.0.0",
  "type": "Mission",
  "data": [
    {
      "name": "pittsburgh",
      "keywords": [],
      "creatorUid": "bob",
      "createTime": "2016-05-03T15:22:44.107Z",
      "uids": [],
      "contents": []
    }
  ]
}
```

## API Response Format - Error

Error responses are encoded in a standard JSON response format with these properties:

- **status**: A textual status code
- **code**: A number error code
- **message**: A human-readable message, often from the server-side exception

```json
{
  "status": "NOT_FOUND",
  "code": 1,
  "message": "Not Found: Mission named 'pittsburw' not found"
}
```

## Response Headers

Except for binary file responses, API Responses are JSON documents, so unless noted otherwise, response headers will include:

```
Content-Type: application/json;charset=UTF-8
```

## Timestamps

Timestamps are in the ISO-8601 format, with milliseconds.

Example: `2016-04-22T15:26:17.260Z`

---

## Mission API

### Resource: Missions

The data sync mission API represents missions and their contents as REST resources (and subresources.)

```
api/missions
```

### GET all missions

```bash
curl "http://<username>:<password>@localhost:8080/Marti/api/missions"
```

Success Response Code: 200 OK

```json
{
  "version": "1.0.0",
  "type": "Mission",
  "data": [
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
        },
        {
          "data": "ANDROID-00:AE:FA:7D:63:EB",
          "timestamp": "2016-05-02T14:11:00.896Z",
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
    },
    {
      "name": "pittsburgh",
      "keywords": [],
      "creatorUid": "ANDROID-C0:BD:D1:5D:4A:3D",
      "createTime": "2016-04-22T17:13:42.437Z",
      "uids": [
        {
          "data": "3b7b1005-f1de-43e6-bb23-7262365753a4",
          "timestamp": "2016-04-22T19:52:31.137Z",
          "creatorUid": "ANDROID-C0:BD:D1:5D:4A:3D"
        }
      ],
      "contents": [
        {
          "data": {
            "keywords": [],
            "mimeType": "image/jpeg",
            "name": "20160421_145952.jpg",
            "submissionTime": "2016-04-22T19:52:30.572Z",
            "submitter": "uploader",
            "uid": "0d69da09-9ec5-49cb-bfe4-5c486f81beee",
            "creatorUid": "ANDROID-C0:BD:D1:5D:4A:3D",
            "hash": "aba4714c08f03b41a990da90013fd0646f1f11fcaa62a2891bb23d2240dfd402",
            "size": 2620798
          },
          "timestamp": "2016-04-22T19:52:30.918Z",
          "creatorUid": ""
        }
      ]
    }
  ]
}
```

### GET (single mission)

```bash
curl "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh"
```

Success Response Code: 200 OK

Error Response Code: 404 Not Found

Error Response Code: 400 Bad Request

### Create Mission

Create a mission with the specified name. Empty request body (all required information is contained in the URI.)

#### Query Parameters

*optional*

- **creatorUid**: Client UID
- **tool**: name of the tool this mission is associated with, i.e, "citrap"

Success Response Code: 201 Created

Example: Create a mission named "Pittsburgh" and a creatorUid of "Bob"

```bash
curl -X PUT "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh?creatorUid=bob"
```

Response Body:

```json
{
  "version": "1.0.0",
  "type": "Mission",
  "data": [
    {
      "name": "pittsburgh",
      "keywords": [],
      "creatorUid": "bob",
      "createTime": "2016-05-03T15:22:44.107Z",
      "uids": [],
      "contents": []
    }
  ]
}
```

The create action is idempotent: if a mission with the specified name already exists, the call will still succeed.

### DELETE (delete mission named 'xyz')

```bash
curl -X DELETE "http://<username>:<password>@localhost:8080/Marti/api/missions/xyz"
```

Success Response Code: 200 OK

```json
{
  "version": "1.0.0",
  "type": "Mission",
  "data": [
    {
      "name": "xyz",
      "createTime": "2016-01-12T20:42:45.755Z",
      "contents": [],
      "keywords": []
    }
  ]
}
```

Error Response: 404 Not Found

```json
{
  "status": "NOT_FOUND",
  "code": 1,
  "message": "Not Found: Mission named 'xyz' does not exist"
}
```

---

### Resource: Mission Content

#### PUT (add mission content)

Enterprise Sync resources (files) can be referenced by hash and / or uid.

by hashes:
```bash
curl -X PUT "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh/contents" \
  -H "Content-Type: application/json" \
  -d '{"hashes" : ["12fg", "789n"]}'
```

by uids:
```bash
curl -X PUT "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh/contents" \
  -H "Content-Type: application/json" \
  -d '{"uids" : ["asdf", "zxv"]}'
```

both:
```bash
curl -X PUT "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh/contents" \
  -H "Content-Type: application/json" \
  -d '{"hashes" : ["12fg", "789n"], "uids" : ["asdf", "zxv"]}'
```

Required Request Headers:
```
Content-Type: application/json
```

JSON Request Body:
```json
{
  "hashes" : [ "<hash>", "<hash>" ],
  "uids" : ["<uid>", "<uid>" ]
}
```

**Request Parameters** *Optional*

- **creatorUid**: Client UID

Success Response Code: 200 OK

#### DELETE (remove mission content by a single hash or uid)

**Query Parameters**

- **hash**: Hash of file to remove.
- **uid**: UID to remove

(either hash or uid must be specified, not both)

*Optional*

- **creatorUid**: Client UID

```bash
curl -v -X DELETE "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh/contents?hash=789n"
curl -v -X DELETE "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh/contents?uid=xyz"
```

Success Response Code: 200 OK

Response Body: JSON representation of the contents of the mission, after the removal.

Error Response Code: 404 Not Found (if the mission doesn't exist)

---

### Resource: Mission Changes

#### GET (get changeset for mission pittsburgh)

```bash
curl "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh/changes"
```

Query Parameters:

- **secago**: time interval - number of seconds in the past, until now.
- **start**: start of time interval. Takes precedence over "secago" if both parameters are specified.
- **end**: end of time interval

Success Response Code: 200 OK

```json
{
  "version": "1.0.0",
  "type": "MissionChange",
  "data": [
    {
      "type": "CREATE_MISSION",
      "missionName": "hamburger",
      "timestamp": "2016-04-19T19:58:21.526Z",
      "creatorUid": "joe1234"
    },
    {
      "type": "ADD_CONTENT",
      "contentResource": {
        "keywords": [],
        "mimeType": "application/x-zip-compressed",
        "name": "gpx_test.zip",
        "submissionTime": "2016-02-18T16:39:31.282Z",
        "submitter": "uploader",
        "uid": "004ac8ddcfe51028676ae81e0cdbf39c62dfeca6a9e55a7de2c92dc379cf6759",
        "hash": "004ac8ddcfe51028676ae81e0cdbf39c62dfeca6a9e55a7de2c92dc379cf6759",
        "size": 3021
      },
      "missionName": "hamburger",
      "timestamp": "2016-04-19T20:01:13.59Z",
      "creatorUid": "joe1234"
    },
    {
      "type": "ADD_CONTENT",
      "contentUid": "abc1234",
      "missionName": "hamburger",
      "timestamp": "2016-04-19T20:09:45.151Z",
      "creatorUid": "joe1234"
    }
  ]
}
```

Change record types include:
- **CREATE_MISSION** -- fields: `type`, `missionName`, `timestamp`, `creatorUid`
- **ADD_CONTENT** (file) -- fields: `type`, `contentResource` (with `keywords`, `mimeType`, `name`, `submissionTime`, `submitter`, `uid`, `hash`, `size`), `missionName`, `timestamp`, `creatorUid`
- **ADD_CONTENT** (CoT UID) -- fields: `type`, `contentUid`, `missionName`, `timestamp`, `creatorUid`

---

### GET mission CoT

```bash
curl "http://<username>:<password>@localhost:8080/Marti/api/missions/pittsburgh/cot"
```

Fetch the latest CoT event for each content UID that is part of the mission.

Success Response Code: 200 OK

Response Body (XML):

```xml
<?xml version='1.0' encoding='UTF-8' standalone='yes'?>
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

Error Response Code: 404 Not Found

---

## Mission Roles

When creating subscriptions or setting roles:
- **MISSION_OWNER** -- full control
- **MISSION_SUBSCRIBER** -- read/write
- **MISSION_READONLY_SUBSCRIBER** -- read only

## Invite Types

When inviting users to a mission, the `type` path parameter specifies how to identify the invitee:
- **clientUid** -- by device UID
- **callsign** -- by callsign
- **userName** -- by username
- **group** -- by group name
- **team** -- by team name
