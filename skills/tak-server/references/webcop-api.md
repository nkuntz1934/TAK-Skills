# TAK Server WebCOP API Definition

## Overview

This document covers the API functionality needed to implement ATAK-like functionality via a Web Browser. There are multiple parts to this API, a set of REST APIs that browsers invoke directly, and then a series of messages that can be used in a WebSocket channel.

## Establishing a WebSocket / STOMP Message Feed

The first step to getting the message feed is establishing a websocket and getting a topic for your user. Once the feed has been established, TAK Server will send relevant SA, Chat, Mission Change and Control messages to the browser client, using the WebSocket channel.

The current implementation infers the user from the client certificate presented, so no embedded additional authentication is needed. When requesting a topic, the websocket client may also specify callsign, team, role and TAK version. Once a topic has been assigned by the server, the topic will be tracked as a subscription in the TAK Server contact list, contact API and subscription list.

The WebSocket connection and topic must be periodically refreshed as directed by REFRESH_TOPIC control messages send from the server to the client.

To request topic creation for your user, invoke this API:

### Get SA Topic

```
curl 'https://[server]:8443/Marti/api/cop/topic?callsign=bob&team=orange&role=HQ&takv=WebCOP:0.1'
```

Success Response Code: 200 OK

```json
{
    "version": "1.0.0",
    "type": "Topic",
    "data": "13876e57d2f24273aaeb2482c12eb5ca"
}
```

*optional parameters: callsign, team, role, takv, clientSeed*

The *clientSeed* parameter allows the browser client to specify a unique random value, which will be incorporated into the topic. If this parameter is provided, then multiple sessions for a single user credential (client cert), for example, on different browsers and computers, will be assigned a different topic (uid). If the parameter not provided, then sessions using the same credential on multiple browsers / computers will share the same topic. Whether or not the *clientSeed* is provided, the topic will remain static.

The 'data' from the response to the 'topic' call is the topic id for your user, which is needed for the next step after the WebSocket is initialized. STOMP is useful for the WebSocket handling. STOMP uses text-based frames.

#### Frame Object

| Property | Type | Notes |
|----------|------|-------|
| command | String | STOMP frame type ("CONNECT", "SEND", "MESSAGE", etc.) |
| headers | JavaScript object | |
| body | String | |

### Connect and Subscribe

```javascript
//Sample code (from WebContent/cop/messaging.js)

var socket = new SockJS('/Marti/api/cop');

    stompClient = Stomp.over(socket);

    getTopicAndSubscribe();

...

var getTopicAndSubscribe = function() {

  $.get("/Marti/api/cop/topic", function(data) {

    var topic = data.data;

    stompClient.connect({}, function(frame) {

      stompClient.subscribe('/topic/' + topic, processMessage);

    });

  });

}
```

'topic' here is the value of the 'data' from the GET call above, and processMessage is a callback for handing data that comes from the server.

The above sample code sends a STOMP frame over the websocket whose raw text looks like this:

```
["SUBSCRIBE\nid:sub-0\ndestination:/topic/13876e57d2f24273aaeb2482c12eb5ca\n\n\u0000"]
```

### Refresh Topic

There are some control messages used to periodically refresh the topic by tearing down the websocket and recreating it. See the processMessage function in WebContent/cop/messaging.js, specifically the 'ControlMessage' case statement.

## SituationAwarenessMessage

When CoT events are published to the backend router, if they match the group-filtering of the current web user they will end up being formatted to come over the WebSocket as STOMP frames. An example in raw text looks like this:

```
["MESSAGE\ndestination:/topic/13876e57d2f24273aaeb2482c12eb5ca\ncontent-type:application/json;charset=UTF-8\nsubscription:sub-0\nmessage-id:k7xzqh80-249482\ncontent-length:279\n\n{\"messageType\":\"SituationAwarenessMessage\",\"uid\":\"142db25e-1be5-36cb-8b5b-dbd1183f2bb2\",\"type\":\"a-f-G-U-C\",\"lat\":42.3803274,\"lon\":-71.13891009999999,\"callsign\":\"admin\",\"hae\":0.0,\"start\":1497366241479,\"time\":1497366241479,\"stale\":1497452641479,\"group\":\"Cyan\",\"role\":\"Team Member\"}\u0000"]
```

Broken out, you can see the STOMP command is "Message", it came from the 'topic' we'd subscribed to earlier, and the body is a JSON object that has the CoT fields broken out in a straightforward way.

The full "SituationAwarenessMessage" type has these fields you can expect:

| Field | Data type |
|-------|-----------|
| messageType | String: "SituationAwarenessMessage" |
| uid | String |
| type | String (CoT formatted) |
| lat | Double |
| lon | Double |
| hae | Double |
| start | CoT Timestamp |
| time | CoT Timestamp |
| stale | CoT Timestamp |
| callsign | String |
| color | String (one of ATAK's 16 colors) |
| role | String ("Team Member", "Team Lead", "HQ") |
| iconsetPath | String (e.g. "34ae1613-9645-4222-a9d2-e5f243dea2865/Buildings/office.png") |

## Icon Resolution

The correct display icon to use should follow ATAK's conventions using GET api/iconurl:

- if 'color' is present, use this API with the value of the color property:
  - `GET https://<server>:8443/Marti/api/iconurl?cotType=a-f-G-U-C-I&groupName=Purple&`
- if 'iconsetPath' is present, then attempt to resolve the icon:
  - `GET https://<server>:8443/Marti/api/iconurl?cotType=a-f-G-U-C-I&iconsetpath=34ae1613-9645-4222-a9d2-e5f243dea2865/Buildings/office.png&`
- else, fall back to CoT type:
  - `GET https://<server>:8443/Marti/api/iconurl?cotType=a-f-G-U-C-I&`

If the iconurl function cannot find the specific icon being asked for by color or iconset, then it will return the CoT 2525B icon. So the 'cotType' parameter is required, others (such as groupName, or iconsetpath) are optional.

## Sending Your Own SA

Once the STOMP WebSocket is initialized, the browser may send his own messages though the websocket. Example Javascript code for this:

```javascript
geolocation.on('change:position', function() {
  console.debug("Geolocation position!");
  var coordinates = geolocation.getPosition();
  if(coordinates) {
    positionFeature.setGeometry(coordinates ? new ol.geom.Point(coordinates) : null);
    var wgsCoord = ol.proj.transform(coordinates, 'EPSG:900913', 'EPSG:4326');
    if(myuid != undefined) {
      stompClient.send("/cop/cop", {}, JSON.stringify({
        'lat' : wgsCoord[1],
        'lon' : wgsCoord[0],
        'type' : 'a-f-G-U-C',
        'uid' : myuid,
        'callsign' : mycallsign
      }));
    }
  }
});
```

This code results in a message sent over the websocket that looks like this:

```
["SEND\ndestination:/cop/cop\ncontent-length:126\n\n{\"lat\":42.3803274,\"lon\":-71.13891009999999,\"type\":\"a-f-G-U-C\",\"uid\":\"142db25e-1be5-36cb-8b5b-dbd1183f2bb2\",\"callsign\":\"admin\"}\u0000"]
```

Note that for a self marker, a consistent UID should be used. Currently only a small selection of CoT fields are supported, namely uid, callsign, type, lat, and lon. This will be expanded to match the full set in the SituationAwarenessMessage for the next code drop.

This same API can be used for the point dropper tool.

## Chat API

### Chat Message Format

As with Situation Awareness messages, chat messages are sent and received over the bi-directional WebSocket channel using the STOMP protocol. The same WebSocket channel may be used for both Situation Awareness messages and Chat messages, establishing a connection and obtaining a topic as described above. When a message is sent from TAK Server to the browser, the STOMP frame type is MESSAGE. When a message is sent from the browser to TAK Server, the STOMP frame type is SEND.

| Field | Data type | Description |
|-------|-----------|-------------|
| messageType | String | "ChatMessage" |
| from | String | Client UID of message sender |
| addresses | String[] | Set of destination addresses, specified in the format: `<type>:<identifier>`. Valid types include: uid &#124;&#124; mission &#124;&#124; group. The identifier is a string specifying the uid, mission or group |
| lat | Number | latitude |
| lon | Number | longitude |
| hae | Number | altitude |
| body | String | Chat message body |
| timestamp | Number | Timestamp when the message was sent |

### Message Routing

Based on the identity of the user accessing the WebCOP API, TAK Server internally tracks group membership. Messages are sent and received to ATAK and WebCop clients based on common group membership. Additional filtering is in addition to the group filtering, further restricting the set of clients who will receive a message.

When sending a message from the WebCOP to TAK Server, messages may be addressed to any combination of ATAK client UIDs, missions, or TAK Server groups, or websocket topics. Websocket topics and ATAK uids may be used interchangeably. A websocket message may be sent to an ATAK client by addressing the message to the uid of the ATAK device. An ATAK device may send a message to a websocket user by addressing the message to the uid (topic) of the websocket client. Messages may also be sent between websocket clients in the same manner. See Mission API documentation, and Contacts and Groups API, below. The Mission API may be used to subscribe WebCOP topics to a mission, and to obtain mission information as REST resources.

Missions may be utilized in the same manner as chatrooms in other chat protocols, such as XMPP.

## Mission Change Notification Message Format

| Field | Data type |
|-------|-----------|
| messageType | String: "MissionChange" |
| missionName | String |
| cotType | String. Change to mission log: "t-x-m-c-l". Other mission change: "t-x-m-c" |
| time | CoT Timestamp |

Once a topic has been subscribed to a mission, it will receive notifications of changes to the mission.

Change notifications will occur for the following events:

- Create Mission
- Delete Mission
- Add Content to Mission (by hash or uid)
- Create, Update or Delete Mission Log Entry. For mission log changes, the cotType will be "t-x-m-c-l"

## Contacts and Groups API

### Resource: contacts

The Contacts API provides information about currently connected TAK Server clients, represented as REST resources. Clients UIDs and callsigns are provided by the API, and may be sorted according to query parameters.

```
api/contacts
```

#### GET all contacts

```
curl "http://<username>:<password>@localhost:8080/Marti/api/contacts/all"

curl "http://<username>:<password>@localhost:8080/Marti/api/contacts/all?sortBy=UID&direction=DESCENDING"
```

Success Response Code: 200 OK

```json
[
    {
        "notes": "Anonymous_329994173",
        "callsign": "anon1",
        "uid": "anon1"
    },
    {
        "notes": "",
        "callsign": "Anonymous_COTRMI",
        "uid": "Anonymous_COTRMI"
    }
]
```

#### Query Parameters

*optional*

sortBy : CALLSIGN or UID

*sort the result list by callsign, or client UID*

direction : ASCENDING or DESCENDING

*specify the ordering of the result list*

### Resource: groups

The Groups API provides information about groups that have been assigned to an actual TAK Server user, or specified in the TAK Server configuration. These groups are persisted in the TAK Server database.

```
api/groups
```

#### GET all groups

```
curl "http://<username>:<password>@localhost:8080/Marti/api/groups/all"
```

Success Response Code: 200 OK

```json
{
    "version": "1.0.0",
    "type": "com.bbn.marti.remote.groups.Group",
    "data": [
        {
            "name": "CN=TAK2,OU=Groups,DC=cam,DC=bbn,DC=com",
            "direction": "OUT",
            "created": "2017-04-01",
            "type": "SYSTEM",
            "bitpos": 9
        },
        {
            "name": "GroupC",
            "direction": "OUT",
            "created": "2017-01-24",
            "type": "SYSTEM",
            "bitpos": 7
        },
        {
            "name": "__ANON__",
            "direction": "OUT",
            "created": "2016-09-27",
            "type": "SYSTEM",
            "bitpos": 0
        },
        {
            "name": "blue",
            "direction": "OUT",
            "created": "2016-09-27",
            "type": "SYSTEM",
            "bitpos": 4
        },
        {
            "name": "c",
            "direction": "OUT",
            "created": "2017-04-01",
            "type": "SYSTEM",
            "bitpos": 12
        },
        {
            "name": "green",
            "direction": "OUT",
            "created": "2016-09-27",
            "type": "SYSTEM",
            "bitpos": 3
        },
        {
            "name": "yellow",
            "direction": "OUT",
            "created": "2016-09-27",
            "type": "SYSTEM",
            "bitpos": 5
        },
        {
            "name": "zoo",
            "direction": "OUT",
            "created": "2017-04-01",
            "type": "SYSTEM",
            "bitpos": 14
        }
    ]
}
```

#### Query Parameters

*none*

## Properties API

The TAK Server properties API can be used as a server-side in-memory repository of user settings and preferences. Settings and preferences can be stored and retrieved on a "per-UID" basis. An arbitrary number of preference keys can be stored per UID, and each key can be associated with one or more values. Deletion methods are provided to clear all data for a UID with a single call, or clear all of the values associated with a single key for a given UID.

### Resource: properties

```
api/properties
```

### PUT a key-value pair for a UID

```
PUT https://gov.atakserver.com:8443/Marti/api/properties/alice
```

Request Body:

A single JSON object associating a key with a value. This call associated the value "HQ" with the key "chatroom". If another PUT is executed for the same UID and key, but with a different value, that value is added to the map for that key, as an additional value.

```json
{"chatroom": "HQ"}
```

Success Response Code: 200 OK

```
{"version":"2","type":"KeyValuePair","data":{"chatroom":"HQ"}}
```

### GET all key-value maps for a UID

```
GET https://gov.atakserver.com:8443/Marti/api/properties/alice/all
```

Success Response Code: 200 OK

```json
{
    "version": "2",
    "type": "Properties",
    "data": {
        "chatroom": [
            "HQ"
        ],
        "team": [
            "Blue",
            "Green"
        ]
    }
}
```

This result shows multiple key-values can be stored for a UID (alice), and multiple values can be stored for a key (team).

If no values are stored for this UID, the result is 404 Not Found, with a JSON error body.

### GET all values for a key, per UID

```
GET https://gov.atakserver.com:8443/Marti/api/properties/alice/team
```

Success Response Code: 200 OK

```json
{
    "version": "2",
    "type": "Properties",
    "data": [
        "Blue",
        "Green"
    ]
}
```

If no values are stored for this UID and key, the result is 404 Not Found, with a JSON error body.

### DELETE all values for a key, per UID

```
DELETE https://gov.atakserver.com:8443/Marti/api/properties/alice/team
```

The key "team" and all values with which it is associated are removed, for the UID "alice"

Success Response Code: 200 OK

### DELETE all data for a UID

```
DELETE https://gov.atakserver.com:8443/Marti/api/properties/alice/all
```

All keys are values that are stored for the UID "alice" are deleted.

Success Response Code: 200 OK
