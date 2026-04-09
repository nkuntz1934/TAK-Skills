# TAK Server API Endpoints

Complete endpoint reference organized by API group. Base URL: `https://<server>:8446` (TLS) or `http://<server>:8080`.

## Table of Contents
- [mission-api](#mission-api)
- [cot-api](#cot-api)
- [cot-query-api](#cot-query-api)
- [cop-view-api](#cop-view-api)
- [contacts-api](#contacts-api)
- [contact-manager-api](#contact-manager-api)
- [metadata-api](#metadata-api)
- [file-manager-api](#file-manager-api)
- [file-configuration-api](#file-configuration-api)
- [security-authentication-api](#security-authentication-api)
- [cert-manager-admin-api](#cert-manager-admin-api)
- [cert-manager-api](#cert-manager-api)
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
- [plugin-manager-api](#plugin-manager-api)
- [qo-s-api](#qo-s-api)
- [video-connection-manager-v-2](#video-connection-manager-v-2)
- [iconset-icon-api](#iconset-icon-api)
- [injection-api](#injection-api)
- [repeater-api](#repeater-api)
- [token-api](#token-api)
- [registration-api](#registration-api)
- [locate-api](#locate-api)
- [ci-trap-report-api](#ci-trap-report-api)
- [vbm-configuration-api](#vbm-configuration-api)
- [profile-admin-api](#profile-admin-api)
- [profile-api](#profile-api)
- [version-api](#version-api)
- [home-api](#home-api)
- [groups-api](#groups-api)
- [uid-search-api](#uid-search-api)
- [sequence-api](#sequence-api)
- [ldap-api](#ldap-api)
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

### External data, feeds, and other (by name)

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| POST | `/Marti/api/missions/{name}/externaldata` | setExternalMissionData | query: creatorUid (**required**). Body: ExternalMissionData. Response: 201 ApiResponseExternalMissionData |
| POST | `/Marti/api/missions/{name}/externaldata/{id}/change` | notifyExternalDataChanged | query: creatorUid, notes. Body: string |
| DELETE | `/Marti/api/missions/{name}/externaldata/{id}` | deleteExternalMissionData | |
| POST | `/Marti/api/missions/{name}/feed` | addFeed | query: creatorUid, dataFeedUid, filterPolygon (string[]), filterCotTypes, filterCallsign |
| DELETE | `/Marti/api/missions/{missionName}/feed/{uid}` | removeFeed | |
| DELETE | `/Marti/api/missions/{missionName}/maplayers/{uid}` | deleteMapLayer | |
| DELETE | `/Marti/api/missions/{childName}/parent` | clearParent | |
| POST | `/Marti/api/missions/{name}/send` | sendMissionArchive | Response: ApiResponseSetMission |
| POST | `/Marti/api/missions/{name}/invite` | sendMissionInvites | query: creatorUid (optional) |
| GET | `/Marti/api/missions/{name}/parent` | getParent | Response: ApiResponseMission |

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
| POST | `/Marti/api/missions/guid/{guid}/externaldata` | setExternalMissionDataByGuid | query: creatorUid. Body: ExternalMissionData. Response: 201 |
| POST | `/Marti/api/missions/guid/{guid}/externaldata/{id}/change` | notifyExternalDataChangedByGuid | query: creatorUid, notes |
| POST | `/Marti/api/missions/guid/{guid}/send` | sendMissionArchiveByGuid | Response: ApiResponseSetMission |
| POST | `/Marti/api/missions/guid/{missionGuid}/invite` | sendMissionInvitesByGuid | query: creatorUid (optional) |
| POST | `/Marti/api/missions/{missionGuid}/feed` | addFeedByGuid | query: creatorUid, dataFeedUid, filterPolygon, filterCotTypes, filterCallsign |

### Global mission endpoints

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/missions` | getAllMissions | query: passwordProtected, defaultRole, tool. Response: ApiResponseListMission |
| DELETE | `/Marti/api/missions` | deleteMissionByGuid | query: guid (**required**), creatorUid, deepDelete. Response: ApiResponseSetMission |
| GET | `/Marti/api/pagedmissions` | getPagedMissions | query: passwordProtected, defaultRole, page, pagesize, tool, sort, nameFilter, uidFilter, ascending. Response: ApiResponseListMission |
| GET | `/Marti/api/missions/all/subscriptions` | getAllMissionSubscriptions |
| GET | `/Marti/api/missions/all/subscriptions/guid` | getAllSubscriptionsWithGuid |
| GET | `/Marti/api/missions/all/logs` | getAllLogEntries |
| GET | `/Marti/api/missions/all/invitations` | getAllMissionInvitations |
| GET | `/Marti/api/missions/invitations` | getAllInvitationsWithPasswords |
| GET | `/Marti/api/missioncount` | countAllMissions | query: passwordProtected, defaultRole, tool |
| GET | `/Marti/api/missions/logs/entries/{id}` | getOneLogEntry |
| DELETE | `/Marti/api/missions/logs/entries/{id}` | deleteLogEntry |
| GET | `/Marti/api/sync/search` | searchSync | query: box, circle, startTime, endTime, minAltitude, maxAltitude, filename, keyword[], mimetype, name, uid, hash, mission, tool |
| GET | `/Marti/api/resources/{hash}` | getResource | Response: ApiResponseListResource |

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
| GET | `/Marti/api/files/{hash}` | getFile_1 | Response: binary |
| DELETE | `/Marti/api/files/{hash}` | deleteFile | |
| HEAD | `/Marti/api/files/{hash}` | getFileHead | Response: ApiResponseMapStringString |
| PUT | `/Marti/api/files/{hash}/metadata` | putMetadata | params: user, expiration, keywords |
| GET | `/Marti/api/files/metadata` | getFileMetadata | query: page (int32, default -1), limit (int32, default -1), mission, missionPackage (boolean), name, sort, ascending (boolean, default true). Response: ApiResponseCollectionMapStringString |
| GET | `/Marti/api/files/metadata/count` | getFileCount | query: mission, missionPackage (boolean). Response: ApiResponseInteger |

---

## security-authentication-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/security/config` | getSecConfig |
| PUT | `/Marti/api/security/config` | modifySecConfig |
| GET | `/Marti/api/security/verifyConfig` | verifyConfig | Response: ApiResponseString |
| GET | `/Marti/api/security/isSecure` | isSecure | Response: ApiResponseString |
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
| GET | `/user-management/api/users-in-group/{group}` | getUsersInGroup | Response: SimpleGroupWithUsersModel |
| GET | `/user-management/api/list-users` | getAllUsers | Response: UsernameModel[] |
| GET | `/user-management/api/list-groupnames` | getAllGroupNames | Response: GroupNameModel[] |
| GET | `/user-management/api/get-groups-for-user/{username}` | getGroupsForUsers | Response: SimpleUserGroupModel |

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
| GET | `/Marti/api/inputs` | getInputMetrics | query: excludeDataFeeds (boolean, default false). Response: ApiResponseSortedSetInputMetric |
| POST | `/Marti/api/inputs` | createInput | Body: Input. Response: ApiResponseInput |
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
| POST | `/Marti/api/retention/restoremission` | restoreMissionFromArchive | Body: integer (int32). Response: ApiResponseString |
| GET | `/Marti/api/retention/missionarchive` | getMissionArchive | Response: ApiResponseString |

---

## federation-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/outgoingconnections` | getOutgoingConnections | Response: ApiResponseSortedSetOutgoingConnectionSummary |
| PUT | `/Marti/api/outgoingconnections` | updateOutgoingConnection | Body: Map<String, FederationOutgoing> |
| POST | `/Marti/api/outgoingconnections` | createOutgoingConnection | Body: FederationOutgoing |
| GET | `/Marti/api/outgoingconnections/{name}` | getOutgoingConnection | Response: ApiResponseFederationOutgoing |
| DELETE | `/Marti/api/outgoingconnections/{name}` | deleteOutgoingConnection | Response: ApiResponseBoolean |
| GET | `/Marti/api/outgoingconnectionstatus/{name}` | getConnectionStatus | Response: ApiResponseConnectionStatus |
| POST | `/Marti/api/outgoingconnectionstatus/{name}` | changeConnectionStatus | query: newStatus (boolean). Response: ApiResponseBoolean |
| GET | `/Marti/api/activeconnections` | getActiveConnections | Response: ApiResponseListConnectionInfoSummary |
| GET | `/Marti/api/clearFederationEvents` | clearDisruptionData | |
| PUT | `/Marti/api/federatemissions/{federateId}` | updateFederateMissions | Body: FederateMissionPerConnectionSettings |
| PUT | `/Marti/api/federatedetails` | updateFederateDetails | Body: Federate |
| DELETE | `/Marti/api/federatedetails/{federateId}` | deleteFederate | Response: ApiResponseBoolean |
| DELETE | `/Marti/api/federatecertificates/{fingerprint}` | deleteFederateCertificateCA | |
| GET | `/Marti/api/federatecertificates` | getFederateCertificates | Response: ApiResponseListCertificateSummary |
| POST | `/Marti/api/federatecertificates` | saveFederateCertificateCA | Body: binary file upload |
| GET | `/Marti/api/federategroupsmap/{federateId}` | getFederateGroupsMap | Response: ApiResponseMapStringString |
| POST | `/Marti/api/federategroupsmap/{federateId}` | addFederateGroupMap | query: remoteGroup, localGroup |
| DELETE | `/Marti/api/federategroupsmap/{federateId}` | removeFederateGroupMap | query: remoteGroup, localGroup |
| POST | `/Marti/api/federategroups` | addFederateGroup | Body: FederateGroupAssociation |
| POST | `/Marti/api/federategroupconfig` | saveFederateGroupConfiguration | |
| POST | `/Marti/api/federatecatokenauth` | setFederateCATokenAuth | Body: object |
| POST | `/Marti/api/federatecahops` | setFederateCAHops | Body: FederateCAHopsAssociation |
| POST | `/Marti/api/federatecagroups` | addFederateCAGroup | Body: FederateCAGroupAssociation |
| GET | `/Marti/api/federate-outbound-groups-hop-limit/{federateId}` | getFederateOutboundGroupsHopLimits | Response: ApiResponseListOutboundGroupHopLimit |
| POST | `/Marti/api/federate-outbound-groups-hop-limit/{federateId}` | addFederateOutboundGroupsHopLimit | Body: object |
| DELETE | `/Marti/api/federate-outbound-groups-hop-limit/{federateId}` | removeFederateOutboundGroupsHopLimit | query: group |
| POST | `/Marti/api/generateFederationJwtToken` | generateFederationJwtToken | Body: JwtTokenRequestModel |
| POST | `/Marti/api/generateAndSaveFederationJwtToken` | generateAndSaveJwtToken | Body: JwtTokenRequestModel |
| GET | `/Marti/api/federates` | getFederates | Response: ApiResponseSortedSetFederate |
| GET | `/Marti/api/fednum` | getNum | Response: int32 |
| GET | `/Marti/api/federateremotegroups/{federateId}` | getFederateRemoteGroups | Response: ApiResponseSortedSetRemoteContact |
| GET | `/Marti/api/federategroups/{federateId}` | getFederateGroups | query: group (**required**), direction (**required**, enum: INBOUND/OUTBOUND/BOTH). Response: ApiResponseListFederateGroupAssociation |
| DELETE | `/Marti/api/federategroups/{federateId}` | removeFederateGroup | query: group, direction (enum: INBOUND/OUTBOUND/BOTH). Response: ApiResponseString |
| GET | `/Marti/api/federatedetails/{id}` | getFederateDetails | Response: ApiResponseFederate |
| GET | `/Marti/api/federatecontacts/{federateId}` | getFederateContacts | Response: ApiResponseSortedSetRemoteContact |
| GET | `/Marti/api/federatecagroups/{caId}` | getFederateCAGroups | Response: ApiResponseListFederateCAGroupAssociation |
| DELETE | `/Marti/api/federatecagroups/{caId}` | removeFederateCAGroup | query: group (**required**), direction (enum: INBOUND/OUTBOUND/BOTH) |

---

## federation-config-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/federationconfig` | getFederationConfig |
| PUT | `/Marti/api/federationconfig` | modifyFederationConfig |
| GET | `/Marti/api/federationconfig/verify` | verifyFederationTruststore | Response: ApiResponseBoolean |

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
| GET | `/Marti/api/excheck/template/{templateUid}` | getTemplate | query: clientUid (**required**) |
| DELETE | `/Marti/api/excheck/template/{templateUid}` | deleteTemplate | query: clientUid (**required**) |
| POST | `/Marti/api/excheck/{templateUid}/start` | startChecklist | query: clientUid, callsign, name, description, startTime, defaultRole (enum) |
| POST | `/Marti/api/excheck/{checklistUid}/stop` | stopChecklist | query: clientUid |
| POST | `/Marti/api/excheck/template` | postTemplate | query: clientUid (**required**), callsign, name, description |
| POST | `/Marti/api/excheck/checklist` | createChecklist | query: clientUid (**required**), defaultRole. Body: string |
| GET | `/Marti/api/excheck/checklist/{checklistUid}` | getChecklist | query: clientUid, secago (int64), token |
| DELETE | `/Marti/api/excheck/checklist/{checklistUid}` | deleteChecklist | query: clientUid (**required**) |
| GET | `/Marti/api/excheck/checklist/{checklistUid}/status` | getChecklistStatus | query: token, clientUid |
| GET | `/Marti/api/excheck/checklist/active` | getChecklist_1 | query: clientUid (**required**) |

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
| GET | `/Marti/api/datafeeds` | getDataFeeds | Response: ApiResponseListDataFeed |
| POST | `/Marti/api/datafeeds` | createDataFeed | Body: DataFeed. Response: ApiResponseDataFeed |
| PUT | `/Marti/api/datafeeds/predicate` | updatePredicateDataFeed | query: updateGroups (boolean, default false). Body: PredicateDataFeed |
| POST | `/Marti/api/datafeeds/predicate` | createPredicateDataFeed | Body: PredicateDataFeed |
| DELETE | `/Marti/api/datafeeds/predicate/{feedGuid}` | deletePredicateDataFeed |

---

## subscription-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/subscriptions/all` | getAllSubscriptions | query: sortBy (CALLSIGN/UID), direction (ASCENDING/DESCENDING), page (int32, default -1), limit (int32, default -1). Response: ApiResponseSetSubscriptionInfo |
| PUT | `/Marti/api/subscriptions/{clientUid}/filter` | setFilter | Body: application/xml Filter |
| DELETE | `/Marti/api/subscriptions/{clientUid}/filter` | deleteFilter | Response: ApiResponseString |
| DELETE | `/Marti/api/subscriptions/delete/{uid}` | deleteSubscription |
| POST | `/Marti/api/subscriptions/incognito/{uid}` | toggleIncognito | Response: string |
| POST | `/Marti/api/subscriptions/add` | addSubscription | Body: tmpStaticSub. Response: ApiResponseSubscriptionInfo |
| GET | `/Marti/api/groups/update/{username}` | groupsUpdated | Response: string |
| PUT | `/Marti/api/groups/activebits` | setActiveGroups |
| PUT | `/Marti/api/groups/active` | setActiveGroups |
| PUT | `/Marti/api/groups/activeForce` | setActiveGroupsForce |

---

## properties-api

| Method | Path | Operation |
|--------|------|-----------|
| PUT | `/Marti/api/properties/{uid}` | storeProperty |
| GET | `/Marti/api/properties/{uid}/all` | getAllPropertyForUid | Response: ApiResponseMapStringCollectionString |
| GET | `/Marti/api/properties/{uid}/{key}` | getPropertyForUid | Response: ApiResponseCollectionString |
| DELETE | `/Marti/api/properties/{uid}/{key}` | clearProperty |
| DELETE | `/Marti/api/properties/{uid}/all` | clearAllProperty |
| GET | `/Marti/api/properties/uids` | getAllPropertyKeys | Response: ApiResponseCollectionString |

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
| PUT | `/Marti/api/qos/read/set` | setRead | Body: RateLimitRule[] |
| PUT | `/Marti/api/qos/read/enable` | enableRead | Body: boolean |
| PUT | `/Marti/api/qos/dos/enable` | enableDOS | Body: boolean |
| PUT | `/Marti/api/qos/delivery/set` | setDelivery | Body: RateLimitRule[] |
| PUT | `/Marti/api/qos/delivery/enable` | enableDelivery | Body: boolean |
| GET | `/Marti/api/qos/ratelimit/read/active` | getActiveReadRateLimit | Response: ApiResponseEntryIntegerInteger |
| GET | `/Marti/api/qos/ratelimit/dos/active` | getActiveDOSRateLimit | Response: ApiResponseEntryIntegerInteger |
| GET | `/Marti/api/qos/ratelimit/delivery/active` | getActiveDeliveryRateLimit | Response: ApiResponseEntryIntegerInteger |
| GET | `/Marti/api/qos/conf` | getQosConf | Response: ApiResponseQos |

---

## video-connection-manager-v-2

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/video` | getVideoCollections | query: protocol (optional). Response: VideoCollections |
| POST | `/Marti/api/video` | createVideoConnection | query: group (string[]). Body: VideoCollections |
| GET | `/Marti/api/video/{uid}` | getVideoConnection |
| PUT | `/Marti/api/video/{uid}` | updateVideoConnection |
| DELETE | `/Marti/api/video/{uid}` | deleteVideoConnection |

---

## iconset-icon-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/iconurl` | getIconUrl | params: cotType (required), iconsetpath, groupName, role, color, medevac, relative |
| GET | `/Marti/api/iconseturl/{uid}` | getIconsetUrl | |
| GET | `/Marti/api/iconset/{uid}` | getAllIconUrlsForIconset | Response: ApiResponseListString |
| GET | `/Marti/api/iconset/all/uid` | getAllIconsetUids | Response: ApiResponseSetString |
| GET | `/Marti/api/iconimage` | getIconImage | params: iconsetpath, cotType, medevac, groupName, role, color, relative. Response: byte |
| GET | `/Marti/api/icon/{uid}/{group}/{name}` | getIcon | Response: byte |
| POST | `/Marti/api/iconset` | postIconsetZip | Body: binary file upload. Response: ApiResponseString |

---

## token-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/token` | getAll | query: expired (boolean, default false). Response: ApiResponseListTokenResult |
| DELETE | `/Marti/api/token/{token}` | revokeToken |
| DELETE | `/Marti/api/token/{tokens}` | revokeTokens |

---

## registration-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| POST | `/register/user` | signUp | params: emailAddress, token |
| POST | `/register/admin/invite` | invite | params: emailAddress, group[] |
| GET | `/register/token/{token}` | confirm | |
| GET | `/register/admin/users` | getAllUsers_1 | Response: TAKUser[] |

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
| GET | `/Marti/api/device/profile` | getAllProfile | Response: ApiResponseListProfile |
| GET | `/Marti/api/device/profile/{name}` | getProfile |
| PUT | `/Marti/api/device/profile/{name}` | updateProfile |
| POST | `/Marti/api/device/profile/{name}` | createProfile |
| PUT | `/Marti/api/device/profile/{name}/file` | addFile |
| GET | `/Marti/api/device/profile/{name}/files` | getFiles | Response: ApiResponseListProfileFile |
| GET | `/Marti/api/device/profile/{name}/file/{id}` | getFile_2 | id is int64. Response: byte |
| DELETE | `/Marti/api/device/profile/{name}/file/{id}` | deleteFile_1 | id is int64 |
| GET | `/Marti/api/device/profile/{name}/directories` | getDirectories | Response: ApiResponseListProfileDirectory |
| PUT | `/Marti/api/device/profile/{name}/directories` | updateDirectories |
| DELETE | `/Marti/api/device/profile/{name}/directories` | deleteDirectories |
| DELETE | `/Marti/api/device/profile/{id}` | deleteProfile | id is int64 |
| POST | `/Marti/api/device/profile/{name}/send` | sendProfile | Body: string array |

---

## ldap-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/ldap` | getLdapGroups | query: groupNameFilter (**required**). Response: ApiResponseSortedSetLdapGroup |
| GET | `/Marti/api/groups/members` | getLdapGroupMembers | query: groupNameFilter (string[], **required**). Response: ApiResponseInteger |
| GET | `/Marti/api/groupprefix` | getGroupPrefix | Response: ApiResponseString |

---

## xmpp-api

| Method | Path | Operation |
|--------|------|-----------|
| GET | `/Marti/api/xmpp/transfer/{uid}/{filename}` | getFile |
| PUT | `/Marti/api/xmpp/transfer/{uid}/{filename}` | putFile |

---

## o-auth-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/logout` | logout | |
| POST | `/logout` | logout | |
| GET | `/token/access` | getAccessToken | Response: ApiResponseString |
| GET | `/login/authserver` | getAuthServerName | Response: ApiResponseString |
| GET | `/login/auth` | handleAuthRequest | |
| GET | `/login/.well-known/openid-configuration` | getOpenIdConfiguration | Response: OpenIdConfiguration |

---

## file-configuration-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/files/api/config` | getFileConfiguration | Response: FileConfigurationModel |
| POST | `/files/api/config` | setFileConfiguration | Body: FileConfigurationModel |

---

## cert-manager-api

Distinct from cert-manager-admin-api. Handles TLS certificate signing and key store generation.

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| POST | `/Marti/api/tls/signClient` | signClientCert | query: clientUid, version. Body: string. Response: byte |
| POST | `/Marti/api/tls/signClient/v2` | signClientCertV2 | query: clientUid, version. Body: string |
| GET | `/Marti/api/tls/makeClientKeyStore` | makeKeyStore | query: cn, clientUid, password (default "atakatak"). Response: byte |
| GET | `/Marti/api/tls/config` | getConfig | Response: string |

---

## injection-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/injectors/cot/uid/{uid}` | getOneCotInjector | Response: ApiResponseCollectionInjectorConfig |
| GET | `/Marti/api/injectors/cot/uid` | getAllCotInjectors | Response: ApiResponseSetInjectorConfig |
| POST | `/Marti/api/injectors/cot/uid` | putCotInjector | Body: InjectorConfig. Response: ApiResponseSetInjectorConfig |
| DELETE | `/Marti/api/injectors/cot/uid` | deleteInjector | query: uid, toInject. Response: ApiResponseSetInjectorConfig |

---

## repeater-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/repeater/period` | getPeriod | Response: ApiResponseInteger |
| POST | `/Marti/api/repeater/period` | setPeriod | Body: integer (int32) |
| GET | `/Marti/api/repeater/list` | getList | Response: ApiResponseListRepeatable |
| GET | `/Marti/api/repeater/remove/{uid}` | remove | Response: ApiResponseBoolean |

---

## plugin-manager-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/plugins/info/all` | getAllPluginInfo | Response: ApiResponseCollectionPluginInfo |
| POST | `/Marti/api/plugins/info/started` | changePluginStartedStatus | query: name, status (boolean). Response: ApiResponseBoolean |
| POST | `/Marti/api/plugins/info/enabled` | changePluginEnabledSetting | query: name, status (boolean). Response: ApiResponseBoolean |
| POST | `/Marti/api/plugins/info/archive` | changePluginArchiveSetting | query: name, archiveEnabled (boolean). Response: ApiResponseBoolean |
| POST | `/Marti/api/plugins/info/all/started` | changeAllPluginStartedStatus | query: status (boolean). Response: ApiResponseBoolean |

---

## cot-query-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api` | getAllSearches | Response: ApiResponseListCotSearch |

---

## version-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/version` | getVersion | Response: string |
| GET | `/Marti/api/version/info` | getVersionInfo | Response: VersionInfo |
| GET | `/Marti/api/version/config` | getVersionConfig | Response: ApiResponseServerConfig |
| GET | `/Marti/api/node/id` | getNodeId | Response: string |

---

## home-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/home` | getHome | Response: string |
| GET | `/Marti/api/ver` | getVer | Response: string |
| GET | `/Marti/api/util/user/roles` | getUserRoles | Response: string[] |
| GET | `/Marti/api/util/isAdmin` | isAdmin | Response: boolean |

---

## groups-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/users/{connectionId}` | getUser | Response: ApiResponseUserGroups |
| GET | `/Marti/api/users/all` | getAllUsers_2 | Response: ApiResponseSortedSetUser |
| GET | `/Marti/api/groups/all` | getAllGroups | query: useCache (boolean, default false), sendLatestSA (boolean, default false). Response: ApiResponseCollectionGroup |
| GET | `/Marti/api/groups/{name}/{direction}` | getGroup | direction enum: IN, OUT. Response: ApiResponseGroup |
| GET | `/Marti/api/groups/user` | getAllGroupsForUser | query: username (**required**). Response: ApiResponseCollectionGroup |
| GET | `/Marti/api/groups/groupCacheEnabled` | getGroupCacheEnabled | Response: ApiResponseBoolean |

---

## uid-search-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/uidsearch` | getUIDResults | query: startDate (**required**), endDate (**required**). Response: ApiResponseListUIDResult |

---

## sequence-api

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/sync/sequence/{key}` | getNextInSequence | Response: string |

---

## profile-api

Distinct from profile-admin-api. Handles profile retrieval and enrollment.

| Method | Path | Operation | Notes |
|--------|------|-----------|-------|
| GET | `/Marti/api/device/profile/{name}/missionpackage` | getProfileMp | Response: byte |
| HEAD | `/Marti/api/device/profile/{name}/missionpackage` | headProfileMp | |
| GET | `/Marti/api/tls/profile/tool/{toolName}/file` | tlsGetProfileDirectoryContent | query: relativePath (string[]), clientUid (**required**) |
| GET | `/Marti/api/tls/profile/enrollment` | getEnrollmentTimeProfiles | query: clientUid (**required**). Response: byte |
