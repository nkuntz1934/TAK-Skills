# TAK Server API Endpoints

Complete endpoint reference organized by API group. Base URL: `https://<server>:8446` (TLS) or `http://<server>:8080`.

## Table of Contents
- [mission-api](#mission-api)
- [cot-api](#cot-api)
- [cop-view-api](#cop-view-api)
- [contacts-api](#contacts-api)
- [contact-manager-api](#contact-manager-api)
- [metadata-api](#metadata-api)
- [file-manager-api](#file-manager-api)
- [security-authentication-api](#security-authentication-api)
- [cert-manager-admin-api](#cert-manager-admin-api)
- [file-user-account-management-api](#file-user-account-management-api)
- [config-api](#config-api)
- [submission-api](#submission-api)
- [retention-api](#retention-api)
- [federation-api](#federation-api)
- [federation-config-api](#federation-config-api)
- [ex-check-api](#ex-check-api)
- [map-layers-api](#map-layers-api)
- [data-feed-api](#data-feed-api)
- [subscription-api](#subscription-api)
- [properties-api](#properties-api)
- [plugin-data-api](#plugin-data-api)
- [qo-s-api](#qo-s-api)
- [video-connection-manager-v-2](#video-connection-manager-v-2)
- [iconset-icon-api](#iconset-icon-api)
- [token-api](#token-api)
- [registration-api](#registration-api)
- [locate-api](#locate-api)
- [ci-trap-report-api](#ci-trap-report-api)
- [vbm-configuration-api](#vbm-configuration-api)
- [profile-admin-api](#profile-admin-api)
- [xmpp-api](#xmpp-api)
- [o-auth-api](#o-auth-api)

---

## mission-api

The largest API group -- covers mission CRUD, content management, subscriptions, invitations, layers, keywords, feeds, and more. Missions can be addressed by name or by GUID.

### Missions by name

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/missions/{name}` | getMission | query: password (string, default ""), changes (boolean, default false), logs (boolean, default false), secago (int64), start (date-time), end (date-time). Response: ApiResponseSetMission |
| PUT | `/Marti/api/missions/{name}` | createMission | query: creatorUid (string, default ""), group (string[], default ["\_\_ANON\_\_"]), description (string), chatRoom (string), baseLayer (string), bbox (string), boundingPolygon (string[]), path (string), classification (string), tool (string, default "public"), password (string), defaultRole (enum: MISSION_OWNER/MISSION_SUBSCRIBER/MISSION_READONLY_SUBSCRIBER), expiration (int64), inviteOnly (boolean, default false), allowGroupChange (boolean, default false). Body: application/json byte. Idempotent. Response: 200 ApiResponseSetMission |
| POST | `/Marti/api/missions/{name}` | createMissionAllowDupe | Same params as PUT plus allowDupe (boolean, default false). Body: application/json byte. Response: 200 ApiResponseSetMission |
| DELETE | `/Marti/api/missions/{name}` | deleteMission | query: creatorUid (string, default ""), deepDelete (boolean, default false). Response: ApiResponseSetMission |

### Content management (by name)

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| PUT | `/Marti/api/missions/{name}/contents` | addMissionContent | query: creatorUid (string, default ""). Body: MissionContent JSON. Response: ApiResponseSetMission |
| DELETE | `/Marti/api/missions/{name}/contents` | removeMissionContent | query: hash (string), uid (string), creatorUid (string, default ""). Response: ApiResponseSetMission |
| PUT | `/Marti/api/missions/{name}/contents/missionpackage` | addMissionPackage | query: creatorUid (string, **required**). Body: application/json byte (required). Response: ApiResponseListMissionChange |
| PUT | `/Marti/api/missions/{name}/content/{hash}/keywords` | addContentKeyword | query: creatorUid (string, default ""). Body: string[] (required) |
| DELETE | `/Marti/api/missions/{name}/content/{hash}/keywords` | clearContentKeywords | query: creatorUid (string, default "") |
| PUT | `/Marti/api/missions/{name}/uid/{uid}/keywords` | addUidKeyword | query: creatorUid (string, default ""). Body: string[] (required) |
| DELETE | `/Marti/api/missions/{name}/uid/{uid}/keywords` | clearUidKeywords | query: creatorUid (string, default "") |

### Keywords & password (by name)

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| PUT | `/Marti/api/missions/{name}/keywords` | setKeywords | query: creatorUid (string, default ""). Body: string[] (required). Response: ApiResponseSetMission |
| DELETE | `/Marti/api/missions/{name}/keywords` | clearKeywords | query: creatorUid (string, default ""). Response: ApiResponseSetMission |
| PUT | `/Marti/api/missions/{name}/password` | setPassword | query: password (string, default ""), creatorUid (string, default "") |
| DELETE | `/Marti/api/missions/{name}/password` | removePassword | query: creatorUid (string, default "") |

### Invitations (by name)

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| PUT | `/Marti/api/missions/{name}/invite/{type}/{invitee}` | inviteToMission | type enum: clientUid, callsign, userName, group, team. query: creatorUid (string, **required**), role (enum: MISSION_OWNER/MISSION_SUBSCRIBER/MISSION_READONLY_SUBSCRIBER, optional) |
| DELETE | `/Marti/api/missions/{name}/invite/{type}/{invitee}` | uninviteFromMission | type enum: clientUid, callsign, userName, group, team. query: creatorUid (string, **required**) |

### Subscriptions & roles (by name)

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/missions/{missionName}/subscription` | getSubscriptionForUser | query: uid (string, default ""). Response: ApiResponseMissionSubscription |
| PUT | `/Marti/api/missions/{missionName}/subscription` | createMissionSubscription | query: uid (string, default ""), topic (string, default ""), password (string, default ""), secago (int64), start (date-time), end (date-time). Response: **201** ApiResponseMissionSubscription |
| DELETE | `/Marti/api/missions/{missionName}/subscription` | deleteMissionSubscription | query: uid (string, default ""), topic (string, default ""), disconnectOnly (boolean, default true) |
| POST | `/Marti/api/missions/{missionName}/subscription` | setSubscriptionRole | query: creatorUid (string, **required**). Body: MissionSubscription[] (required) |
| GET | `/Marti/api/missions/{missionName}/role` | getMissionRoleFromToken | Response: ApiResponseMissionRole |
| PUT | `/Marti/api/missions/{missionName}/role` | setMissionRole | |
| GET | `/Marti/api/missions/{missionName}/subscriptions` | getMissionSubscriptions | |
| GET | `/Marti/api/missions/{missionName}/subscriptions/roles` | getMissionSubscriptionRoles | |

### Queries (by name)

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/missions/{name}/changes` | getMissionChanges |
| GET | `/Marti/api/missions/{name}/cot` | getLatestMissionCotEvents |
| GET | `/Marti/api/missions/{name}/kml` | getKml |
| GET | `/Marti/api/missions/{name}/contacts` | getMissionContacts |
| GET | `/Marti/api/missions/{name}/children` | getChildren |
| GET | `/Marti/api/missions/{name}/archive` | getMissionArchive |
| GET | `/Marti/api/missions/{missionName}/token` | getReadOnlyAccessToken |
| GET | `/Marti/api/missions/{missionName}/log` | getLogEntry |
| GET | `/Marti/api/missions/{missionName}/layers/{layerUid}` | getMissionLayer |
| GET | `/Marti/api/missions/{missionName}/invitations` | getMissionInvitations |
| PUT | `/Marti/api/missions/{name}/expiration` | setExpiration_1 | query: expiration (int64, optional) |

### Other (by name)

| Method | Path | Operation |
|--------|------|-----------|
| DELETE | `/Marti/api/missions/{name}/externaldata/{id}` | deleteExternalMissionData |
| DELETE | `/Marti/api/missions/{missionName}/feed/{uid}` | removeFeed |
| DELETE | `/Marti/api/missions/{missionName}/maplayers/{uid}` | deleteMapLayer |
| DELETE | `/Marti/api/missions/{childName}/parent` | clearParent |

### Missions by GUID

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/missions/guid/{guid}` | getMissionByGuid |
| PUT | `/Marti/api/missions/guid/{guid}/contents` | addMissionContentByGuid |
| DELETE | `/Marti/api/missions/guid/{guid}/contents` | removeMissionContentByGuid |
| PUT | `/Marti/api/missions/guid/{guid}/expiration` | setExpirationByGuid |
| PUT | `/Marti/api/missions/guid/{guid}/password` | setPasswordByGuid |
| DELETE | `/Marti/api/missions/guid/{guid}/password` | removePasswordByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/subscriptions` | getSubscriptionsByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/subscriptions/roles` | getSubscriptionRolesByGuid |
| PUT | `/Marti/api/missions/guid/{missionGuid}/subscription` | createSubscriptionByGuid |
| DELETE | `/Marti/api/missions/guid/{missionGuid}/subscription` | deleteSubscriptionByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/role` | getRoleByGuid |
| PUT | `/Marti/api/missions/guid/{missionGuid}/role` | setRoleByGuid |
| PUT | `/Marti/api/missions/guid/{missionGuid}/maplayers` | updateMapLayerByGuid |
| POST | `/Marti/api/missions/guid/{missionGuid}/maplayers` | createMapLayerByGuid |
| DELETE | `/Marti/api/missions/{missionGuid}/maplayers/{uid}` | deleteMapLayerByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/layers` | getLayersByGuid |
| PUT | `/Marti/api/missions/guid/{missionGuid}/layers` | createLayerByGuid |
| DELETE | `/Marti/api/missions/guid/{missionGuid}/layers` | deleteLayerByGuid |
| PUT | `/Marti/api/missions/guid/{missionGuid}/layers/{layerUid}/position` | setLayerPositionByGuid |
| PUT | `/Marti/api/missions/guid/{missionGuid}/layers/{layerUid}/name` | setLayerNameByGuid |
| PUT | `/Marti/api/missions/guid/{missionGuid}/layers/parent` | setLayerParentByGuid |
| PUT | `/Marti/api/missions/guid/{missionGuid}/invite/{type}/{invitee}` | inviteByGuid |
| DELETE | `/Marti/api/missions/guid/{missionGuid}/invite/{type}/{invitee}` | uninviteByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/invitations` | getInvitationsByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/cot` | getCotByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/contacts` | getContactsByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/children` | getChildrenByGuid |
| GET | `/Marti/api/missions/guid/{missionGuid}/kml` | getKmlByGuid |
| PUT | `/Marti/api/missions/guid/{childGuid}/parent/guid/{parentGuid}` | setParentByGuid |
| DELETE | `/Marti/api/missions/guid/{childGuid}/parent` | clearParentByGuid |
| DELETE | `/Marti/api/missions/guid/{guid}/externaldata/{id}` | deleteExternalDataByGuid |

### Global mission endpoints

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/missions/all/subscriptions` | getAllMissionSubscriptions |
| GET | `/Marti/api/missions/all/subscriptions/guid` | getAllSubscriptionsWithGuid |
| GET | `/Marti/api/missions/all/logs` | getAllLogEntries |
| GET | `/Marti/api/missions/all/invitations` | getAllMissionInvitations |
| GET | `/Marti/api/missions/invitations` | getAllInvitationsWithPasswords |
| GET | `/Marti/api/missioncount` | countAllMissions |
| GET | `/Marti/api/missions/logs/entries/{id}` | getOneLogEntry |
| DELETE | `/Marti/api/missions/logs/entries/{id}` | deleteLogEntry |

---

## cot-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/cot/matchId` | getCotEventsByTimeAndBbox | query: search |

---

## cop-view-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/cops` | getAllCopMissions | params: path, offset, size |
| GET | `/Marti/api/cops/hierarchy` | getHierarchy | → ApiResponseSetCopHierarchyNode |

---

## contacts-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/contacts/all` | getAllContactsLite | params: sortBy (CALLSIGN/UID), direction (ASCENDING/DESCENDING), noFederates |
| GET | `/Marti/api/contacts/all/lite` | getAllContactsLiteWithUser | |
| GET | `/Marti/api/contacts/all/full` | getAllContacts | → RemoteSubscription array |

---

## contact-manager-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/clientEndPoints` | getClientEndpoints | params: secAgo, showCurrentlyConnectedClients, showMostRecentOnly, group |

---

## metadata-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/Marti/api/sync/metadata/{hash}/{metadata}` | setMetadata |
| PUT | `/Marti/api/sync/metadata/{hash}/keywords` | setMetadataKeywords |
| PUT | `/Marti/api/sync/metadata/{hash}/expiration` | setExpiration |

---

## file-manager-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| PUT | `/Marti/api/files/{hash}/metadata` | putMetadata | params: user, expiration, keywords |

---

## security-authentication-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/security/config` | getSecConfig |
| PUT | `/Marti/api/security/config` | modifySecConfig |
| GET | `/Marti/api/authentication/config` | getAuthConfig |
| PUT | `/Marti/api/authentication/config` | modifyAuthConfig |
| POST | `/Marti/api/authentication/config` | testAuthConfig |

---

## cert-manager-admin-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/certadmin/cert` | getAll | params: username → ApiResponseListTakCert |
| GET | `/Marti/api/certadmin/cert/{hash}` | getCertificate | → ApiResponseTakCert |
| DELETE | `/Marti/api/certadmin/cert/{hash}` | revokeCertificate | |
| GET | `/Marti/api/certadmin/cert/{hash}/download` | downloadCertificate | |
| GET | `/Marti/api/certadmin/cert/active` | getActive | |
| GET | `/Marti/api/certadmin/cert/expired` | getExpired | |
| GET | `/Marti/api/certadmin/cert/revoked` | getRevoked | |
| GET | `/Marti/api/certadmin/cert/replaced` | getReplaced | |
| GET | `/Marti/api/certadmin/cert/download/{ids}` | downloadCertificates | binary |
| DELETE | `/Marti/api/certadmin/cert/revoke/{ids}` | revokeCertificates | |
| DELETE | `/Marti/api/certadmin/cert/delete/{ids}` | deleteCertificates | |

---

## file-user-account-management-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/user-management/api/update-groups` | updateGroupsForUser |
| PUT | `/user-management/api/update-group-users` | updateUsersForGroup |
| PUT | `/user-management/api/change-user-password` | changeUserPassword |
| POST | `/user-management/api/new-users` | createFileUsersInBulk |
| POST | `/user-management/api/new-user` | createOrUpdateFileUser |
| DELETE | `/user-management/api/delete-user/{username}` | deleteUser |

---

## config-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/config` | getCoreConfig |
| GET | `/Marti/api/cachedInputConfig` | getCachedInputConfig |
| GET | `/Marti/api/cachedConfig` | getCachedCoreConfig |

---

## submission-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/Marti/api/inputs/{id}` | modifyInput |
| GET | `/Marti/api/inputs/{name}` | getInputMetric |
| DELETE | `/Marti/api/inputs/{name}` | deleteInput |
| GET | `/Marti/api/inputs/config` | getConfigInfo |
| PUT | `/Marti/api/inputs/config` | modifyConfigInfo |
| GET | `/Marti/api/inputs/storeForwardChat/enabled` | isStoreForwardChatEnabled |
| PUT | `/Marti/api/inputs/storeForwardChat/enable` | enableStoreForwardChat |
| PUT | `/Marti/api/inputs/storeForwardChat/disable` | disableStoreForwardChat |
| GET | `/Marti/api/datafeeds/{name}` | getDataFeed |
| PUT | `/Marti/api/datafeeds/{name}` | modifyDataFeed |
| DELETE | `/Marti/api/datafeeds/{name}` | deleteDataFeed |

---

## retention-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/retention/service/schedule` | getRetentionServiceSchedule |
| PUT | `/Marti/api/retention/service/schedule` | setRetentionServiceSchedule |
| PUT | `/Marti/api/retention/resource/{name}/expiry/{time}` | scheduleResourceExpiration |
| GET | `/Marti/api/retention/policy` | getRetentionPolicy |
| PUT | `/Marti/api/retention/policy` | updateRetentionPolicy |
| GET | `/Marti/api/retention/missionarchiveconfig` | getMissionArchiveConfig |
| PUT | `/Marti/api/retention/missionarchiveconfig` | updateMissionArchiveConfig |
| PUT | `/Marti/api/retention/mission/{name}/expiry/{time}` | scheduleMissionExpiration |

---

## federation-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/outgoingconnections` | getOutgoingConnections |
| PUT | `/Marti/api/outgoingconnections` | updateOutgoingConnection |
| POST | `/Marti/api/outgoingconnections` | createOutgoingConnection |
| GET | `/Marti/api/activeconnections` | getActiveConnections |
| GET | `/Marti/api/clearFederationEvents` | clearDisruptionData |
| PUT | `/Marti/api/federatemissions/{federateId}` | updateFederateMissions |
| PUT | `/Marti/api/federatedetails` | updateFederateDetails |
| DELETE | `/Marti/api/federatedetails/{federateId}` | deleteFederate |
| DELETE | `/Marti/api/federatecertificates/{fingerprint}` | deleteFederateCertificateCA |

---

## federation-config-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/federationconfig` | getFederationConfig |
| PUT | `/Marti/api/federationconfig` | modifyFederationConfig |

---

## ex-check-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/excheck/template/{templateUid}/task/{taskUid}` | getTemplateTask |
| PUT | `/Marti/api/excheck/template/{templateUid}/task/{taskUid}` | addEditTemplateTask |
| DELETE | `/Marti/api/excheck/template/{templateUid}/task/{taskUid}` | deleteTemplateTask |
| GET | `/Marti/api/excheck/checklist/{checklistUid}/task/{taskUid}` | getChecklistTask |
| PUT | `/Marti/api/excheck/checklist/{checklistUid}/task/{taskUid}` | addEditChecklistTask |
| DELETE | `/Marti/api/excheck/checklist/{checklistUid}/task/{taskUid}` | deleteChecklistTask |
| PUT | `/Marti/api/excheck/checklist/{checklistUid}/mission/{missionName}` | addMissionReference |
| DELETE | `/Marti/api/excheck/checklist/{checklistUid}/mission/{missionName}` | removeMissionReference |

---

## map-layers-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/Marti/api/maplayers` | updateMapLayer |
| POST | `/Marti/api/maplayers` | createMapLayer |
| GET | `/Marti/api/maplayers/{uid}` | getMapLayerForUid |
| DELETE | `/Marti/api/maplayers/{uid}` | deleteMapLayer |
| GET | `/Marti/api/maplayers/all` | getAllMapLayers |

---

## data-feed-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/Marti/api/datafeeds/predicate` | updatePredicateDataFeed |
| POST | `/Marti/api/datafeeds/predicate` | createPredicateDataFeed |
| DELETE | `/Marti/api/datafeeds/predicate/{feedGuid}` | deletePredicateDataFeed |

---

## subscription-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/Marti/api/subscriptions/{clientUid}/filter` | setFilter |
| DELETE | `/Marti/api/subscriptions/{clientUid}/filter` | deleteFilter |
| DELETE | `/Marti/api/subscriptions/delete/{uid}` | deleteSubscription |
| PUT | `/Marti/api/groups/activebits` | setActiveGroups |
| PUT | `/Marti/api/groups/active` | setActiveGroups |
| PUT | `/Marti/api/groups/activeForce` | setActiveGroupsForce |

---

## properties-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/Marti/api/properties/{uid}` | storeProperty |
| GET | `/Marti/api/properties/{uid}/all` | getAllProperties |
| GET | `/Marti/api/properties/{uid}/{key}` | getPropertyValues |
| DELETE | `/Marti/api/properties/{uid}/{key}` | deletePropertyKey |
| DELETE | `/Marti/api/properties/{uid}/all` | deleteAllProperties |

---

## plugin-data-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/plugins/{name}/submit` | requestFromPlugin |
| PUT | `/Marti/api/plugins/{name}/submit` | submitToPluginUTF8 |
| POST | `/Marti/api/plugins/{name}/submit` | updateInPlugin |
| DELETE | `/Marti/api/plugins/{name}/submit` | deleteFromPlugin |
| PUT | `/Marti/api/plugins/{name}/submit/result` | submitWithResult |

---

## qo-s-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/Marti/api/qos/read/set` | setRead |
| PUT | `/Marti/api/qos/read/enable` | enableRead |
| PUT | `/Marti/api/qos/dos/enable` | enableDOS |
| PUT | `/Marti/api/qos/delivery/set` | setDelivery |
| PUT | `/Marti/api/qos/delivery/enable` | enableDelivery |

---

## video-connection-manager-v-2

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/video/{uid}` | getVideoConnection |
| PUT | `/Marti/api/video/{uid}` | updateVideoConnection |
| DELETE | `/Marti/api/video/{uid}` | deleteVideoConnection |

---

## iconset-icon-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/iconurl` | getIconUrl | params: cotType (required), iconsetpath, groupName, role, color, medevac, relative |
| GET | `/Marti/api/iconseturl/{uid}` | getIconsetUrl | |

---

## token-api

| Method | Path | Operation |
|--------|------|-----------|
| DELETE | `/Marti/api/token/{token}` | revokeToken |
| DELETE | `/Marti/api/token/{tokens}` | revokeTokens |

---

## registration-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| POST | `/register/user` | signUp | params: emailAddress, token |
| POST | `/register/admin/invite` | invite | params: emailAddress, group[] |

---

## locate-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| POST | `/locate/api` | locate | params: latitude, longitude, timestamp, left, bottom, right, top, isFiltered |

---

## ci-trap-report-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/citrap` | getReports | params: keywords, bbox, startTime, endTime, maxReportCount, type, callsign, subscribe, clientUid |
| POST | `/Marti/api/citrap` | postReport | query: clientUid (required). Body: byte |
| GET | `/Marti/api/citrap/{id}` | getReport | |
| PUT | `/Marti/api/citrap/{id}` | putReport | |
| DELETE | `/Marti/api/citrap/{id}` | deleteReport | |
| POST | `/Marti/api/citrap/{id}/attachment` | addAttachment | query: clientUid. Body: byte |

---

## vbm-configuration-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/vbm/api/config` | getVBMConfiguration |
| POST | `/vbm/api/config` | setVBMConfiguration |
| GET | `/vbm/api/classification` | getVBMNetworkClassification |

---

## profile-admin-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/device/profile/{name}` | getProfile |
| PUT | `/Marti/api/device/profile/{name}` | updateProfile |
| POST | `/Marti/api/device/profile/{name}` | createProfile |
| PUT | `/Marti/api/device/profile/{name}/file` | addFile |
| PUT | `/Marti/api/device/profile/{name}/directories` | updateDirectories |
| DELETE | `/Marti/api/device/profile/{id}` | deleteProfile | id is int64 |
| POST | `/Marti/api/device/profile/{name}/send` | sendProfile | Body: string array |

---

## xmpp-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/xmpp/transfer/{uid}/{filename}` | getFile |
| PUT | `/Marti/api/xmpp/transfer/{uid}/{filename}` | putFile |

---

## o-auth-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/logout` | logout |
| POST | `/logout` | logout |
