# TAK Server Schemas

Complete schema reference from the OpenAPI 3.1.0 specification. Schemas use XML namespace `http://bbn.com/marti/xml/config` for configuration objects.

## Table of Contents
- [Top-Level Configuration](#top-level-configuration)
- [Network & Connectivity](#network--connectivity)
- [Authentication & Security](#authentication--security)
- [Mission & Content Models](#mission--content-models)
- [Federation Models](#federation-models)
- [Subscription & Contact Models](#subscription--contact-models)
- [Filter & QoS Models](#filter--qos-models)
- [Certificate Models](#certificate-models)
- [API Response Wrappers](#api-response-wrappers)
- [Other Models](#other-models)

---

## Top-Level Configuration

### Configuration
Top-level server config object. Required: auth, buffer, dissemination, filter, network, repeater, repository, security, submission, subscription.

- `network` -- $ref Network
- `auth` -- $ref Auth
- `submission` -- $ref Submission
- `subscription` -- $ref Subscription
- `repository` -- $ref Repository
- `repeater` -- $ref Repeater
- `filter` -- $ref Filter
- `buffer` -- $ref Buffer
- `dissemination` -- $ref Dissemination
- `certificateSigning` -- $ref CertificateSigning
- `logging` -- $ref Logging
- `security` -- $ref Security
- `ferry` -- $ref Ferry
- `async` -- $ref Async
- `federation` -- $ref Federation
- `geocache` -- $ref Geocache
- `citrap` -- $ref Citrap
- `xmpp` -- $ref Xmpp
- `plugins` -- $ref Plugins
- `cluster` -- $ref Cluster
- `docs` -- $ref Docs
- `email` -- $ref Email
- `locate` -- $ref Locate
- `vbm` -- $ref Vbm
- `profile` -- $ref Profile
- `forceLowConcurrency` -- boolean

### Repository
- `connection` -- $ref Connection (required)
- `enable` -- boolean
- `numDbConnections` -- int32
- `connectionPoolAutoSize` -- boolean
- `primaryKeyBatchSize`, `insertionBatchSize` -- int32
- `archive` -- boolean
- `iconsetDir` -- string
- `enableCallsignAudit` -- boolean
- `contactCacheMaxClearRateSeconds` -- int32
- `dbTimeoutMs` -- int64
- `dbConnectionMaxLifetimeMs`, `dbConnectionMaxIdleMs` -- int32
- `poolScaleFactor` -- int32

### Connection
Database connection configuration.
- `url`, `username`, `password` -- string
- `sslEnabled` -- boolean
- `sslMode`, `sslCert`, `sslKey`, `sslRootCert` -- string
- `queryBufferMaxMemoryPercentage` -- int32

### Buffer
Required: latestSA, queue.
- `queue` -- $ref Queue
- `latestSA` -- $ref LatestSA

### Queue
- `priority` -- $ref Priority (levels: int32)
- `pubSubCapacity`, `outboundCapacity`, `inboundCapacity` -- int32
- `codecWrapperCapacity`, `tcpWriteQueueCapacity` -- int32
- `disconnectOnFull` -- boolean
- `maxWriteQueueSize`, `defaultExecQueueSize`, `defaultMaxPoolSize` -- int32
- `defaultMaxPoolFactor`, `defaultCoreMaxPoolFactor` -- int32
- `messageWriteQueueSize`, `messageWriteExecutorQueueSize` -- int32
- `codecViewPendingCapacity` -- int32
- `queueSizeInitial`, `queueSizeIncrement`, `queueSizeMaxCapacity` -- int32
- `coreExecutorCapacity` -- int32
- `throwOnAssertionFail`, `disconnectOnPendingExceeded` -- boolean
- `flushInterval` -- int32
- `websocketSendBufferSizeLimit`, `websocketMaxBinaryMessageBufferSize` -- int32
- `websocketMaxSessionIdleTimeout` -- int64
- `websocketSendTimeoutMs` -- int32
- `missionUidLimit`, `missionContentLimit`, `missionConcurrentDownloadLimit` -- int32
- `nearCacheMaxSize`, `cotCacheMaxSize`, `cotCacheBatchSize`, `cotCacheMaxMemorySize` -- int32
- `springCacheMaxSize`, `springCacheBatchSize`, `springCacheMaxMemorySize` -- int32
- `springCacheSizeScalingFactor` -- int32
- `onHeapEnabled` -- boolean
- `cacheLastTouchedExpiryMinutes` -- int32
- `enableCacheGroup`, `enableCacheGroupPerName` -- boolean
- `enableGetAllMissionsCacheWarmer`, `enableIndividualHydratedMissionsCacheWarmer` -- boolean
- `cacheCotInRepository` -- boolean
- `messageTimestampCacheSizeItems` -- int64
- `enableStoreForwardChat` -- boolean
- `storeForwardQueryBufferMs`, `storeForwardSendBufferMs` -- int64
- `enableClientEndpointCache` -- boolean
- `contactCacheUpdateRateLimitSeconds`, `contactCacheRecencyLimitSeconds` -- int64
- `pluginDatafeedCacheSeconds`, `caffeineFileCacheSeconds` -- int32
- `groupDescriptionCacheSeconds` -- int32
- `missionCacheLockTimeoutMilliseconds` -- int32

### LatestSA
- `enable` -- boolean
- `validateClientUid` -- boolean

### Submission
- `ignoreStaleMessages` -- boolean
- `validateXml` -- boolean
- `dropMessagesIfAnyServiceIsFull` -- boolean

### Subscription
- `reloadPersistent` -- boolean
- `static` -- array of $ref Static

### Static
- `filtergroup` -- string array
- `filter` -- $ref Filter
- `name` -- string (xml: _name)
- `protocol`, `address` -- string
- `port` -- int32
- `xpath` -- string
- `federated` -- boolean
- `iface` -- string

### Repeater
- `repeatableType` -- array of $ref RepeatableType
- `enable` -- boolean
- `periodMillis`, `staleDelayMillis` -- int32
- `maxAllowedRepeatables` -- int32

### RepeatableType
- `initiateTest` -- string (xml: initiate-test)
- `cancelTest` -- string (xml: cancel-test)
- `name` -- string (xml: _name)

### Dissemination
- `smartRetry`, `boundedSubscriptionWrite`, `enabled` -- boolean

### Logging
- `jsonFormatEnabled`, `auditLoggingEnabled`, `prettyLoggingEnabled` -- boolean
- `lineSeparatorIncluded`, `doubleSpaced`, `httpAccessEnabled` -- boolean

### Async
- `enable` -- boolean

### Docs
- `adminOnly` -- boolean

### Plugins
- `usePluginMessageQueue` -- boolean
- `allowPluginContacts` -- boolean
- `pluginContactCleanupFrequencySeconds` -- int32

### Citrap
- `enableNotifications` -- boolean
- `notificationCot`, `nonsubscriberCotFilter` -- string
- `searchRadius`, `searchSecago` -- int32

### Geocache
- `enable` -- boolean
- `connectId` -- string
- `maxTilesPerGoal`, `maxSizeOfCacheInMb` -- int32
- `cacheDir` -- string
- `numThreads` -- int32

### Cluster
- `enabled` -- boolean
- `natsURL`, `natsClusterID` -- string
- `kubernetes`, `cacheConfig` -- boolean
- `metricsIntervalSeconds` -- int32

### Locate
- `enabled`, `requireLogin` -- boolean
- `cotType`, `group` -- string
- `broadcast`, `addToMission` -- boolean
- `mission` -- string

---

## Network & Connectivity

### Network
- `filter` -- $ref Filter
- `input` -- array of $ref Input
- `datafeed` -- array of $ref DataFeed
- `connector` -- array of $ref Connector
- `announce` -- $ref Announce
- `multicastTTL` -- int32
- `serverId` -- string
- `enterpriseSyncSizeLimitMB`, `enterpriseSyncSizeUploadTimeoutMillis`, `enterpriseSyncSizeDownloadTimeoutMillis` -- int32
- `missionPackageAutoExtractSizeLimitMB`, `httpSessionTimeoutMinutes` -- int32
- `extWebContentDir`, `takServerHost` -- string
- `useLinuxEpoll`, `allowAllOrigins`, `enableMSTS` -- boolean
- `esyncEnableCache` -- int32
- `esyncEnableCotFilter` -- boolean
- `esyncCotFilter`, `version`, `webCiphers` -- string
- `tomcatPoolIdleToMax` -- boolean
- `tomcatMaxPool`, `tomcatPoolMultiplier` -- int32
- `cloudwatchNamespace` -- string
- `cloudwatchMetricsBatchSize` -- int32
- `cloudwatchEnable` -- boolean
- `cloudwatchName` -- string
- `missionCopTool` -- string
- `reportTimeoutSeconds`, `reportTimeoutCheckIntervalSeconds` -- int32
- `alwaysArchiveMissionCot` -- boolean
- `missionCreateGroupsRegex` -- string
- `missionDeleteRequiresOwner`, `missionUseGroupsForContents`, `missionAllowGroupChange` -- boolean
- `missionStrictUidMissionMembership`, `missionBrokerUidAddsFromApi` -- boolean

### Connector
- `header` -- array of $ref Header
- `port` -- int32
- `tls`, `useFederationTruststore` -- boolean
- `clientAuth` -- string
- `allowBasicAuth` -- boolean
- `crlFile` -- string
- `_name` -- string
- `keystore`, `keystoreFile`, `keystorePass` -- string
- `truststore`, `truststoreFile`, `truststorePass` -- string
- `enableAdminUI`, `enableWebtak`, `enableNonAdminUI` -- boolean
- `allowOrigins`, `allowMethods`, `allowHeaders` -- string
- `allowCredentials` -- boolean

### Announce
- `enable` -- boolean
- `group` -- string
- `port`, `interval` -- int32
- `ip`, `svctype` -- string

### Vbm (Virtual Battle Map)
- `enabled` -- boolean
- `disableSASharing`, `disableChatSharing` -- boolean
- `returnCopsWithPublicMissions` -- boolean
- `ismUrl` -- string
- `ismConnectTimeoutSeconds`, `ismReadTimeoutSeconds` -- int64
- `ismStrictEnforcing` -- boolean

### Xmpp
- `xmppHost`, `takServerHost` -- string
- `xmppPort`, `takServerPort` -- int32
- `xmppSharedSecret` -- string
- `xmppComponentRetryCount`, `xmppComponentRetryDelay` -- int32

### Email
- `whitelist` -- array of $ref Whitelist
- `logAlertsExtension` -- string array
- `host` -- string
- `port` -- int32
- `username`, `password`, `from`, `supportName`, `supportEmail` -- string
- `registrationPort` -- int32
- `logAlertsEnabled` -- boolean
- `logAlertsTo`, `logAlertsSubject` -- string

### Ferry
- `endpoint` -- array of $ref Endpoint
- `enable` -- boolean
- `stale` -- int32
- `webserver` -- string

---

## Authentication & Security

### Auth
- `ldap` -- $ref Ldap
- `file` -- $ref File (location: string)
- `oauth` -- $ref Oauth
- `x509Groups`, `x509GroupsDefaultRDN`, `x509AddAnonymous`, `x509UseGroupCache` -- boolean
- `x509UseGroupCacheDefaultActive`, `x509UseGroupCacheDefaultUpdatesActive` -- boolean
- `x509UseGroupCacheRequiresActiveGroup`, `x509UseGroupCacheRequiresExtKeyUsage` -- boolean
- `x509CheckRevocation`, `x509TokenAuth`, `x509AssignAdminAllGroups` -- boolean
- `default` -- string
- `dnusernameExtractorRegex` -- string

### Oauth
- `client` -- array of $ref Client
- `authServer` -- array of $ref AuthServer
- `oauthAddAnonymous` -- boolean
- `oauthUseGroupCache` -- boolean
- `loginWithEmail` -- boolean
- `useTakServerLoginPage` -- boolean

### Client (OAuth)
- `clientId`, `secret`, `redirectUri`, `resourceIds`, `scope` -- string
- `authorizedGrantTypes`, `authorities`, `autoapprove` -- string
- `refreshTokenValidity` -- int32

### AuthServer
- `name`, `issuer`, `clientId`, `secret`, `redirectUri`, `scope` -- string
- `authEndpoint`, `tokenEndpoint` -- string
- `accessTokenName`, `refreshTokenName` -- string
- `trustAllCerts` -- boolean

### Ldap
- `filtergroup` -- array
- `url`, `userstring` -- string
- `updateinterval` -- int32
- `groupprefix`, `groupNameExtractorRegex` -- string
- `style` -- enum: AD, DS
- `ldapSecurityType` -- enum: NONE, SIMPLE
- `serviceAccountDN`, `serviceAccountCredential` -- string
- `groupObjectClass`, `userObjectClass`, `groupBaseRDN`, `userBaseRDN` -- string
- `x509Groups`, `x509AddAnonymous` -- boolean
- `matchGroupInChain`, `nestedGroupLookup`, `postMissionEventsAsPublic` -- boolean
- `ldapsTruststore`, `ldapsTruststoreFile`, `ldapsTruststorePass` -- string
- `readOnlyGroup`, `readGroupSuffix`, `writeGroupSuffix` -- string
- `loginWithEmail` -- boolean
- `callsignAttribute`, `colorAttribute`, `roleAttribute` -- string
- `enableConnectionPool` -- boolean
- `connectionPoolTimeout`, `dnAttributeName`, `nameAttr`, `nameAttrAD` -- string

### Security
- `tls` -- $ref Tls
- `missionTls` -- array of $ref MissionTls

### Tls
- `crl` -- array of $ref Crl
- `keystore`, `keystoreFile`, `keystorePass` -- string
- `truststore`, `truststoreFile`, `truststorePass` -- string
- `context`, `ciphers`, `keymanager` -- string
- `enableOCSP` -- boolean

### MissionTls
- `keystore`, `keystoreFile`, `keystorePass` -- string

### Crl
- `name`, `crlFile` -- string

### TAKServerCAConfig
- `keystore`, `keystoreFile`, `keystorePass` -- string
- `validityDays`, `validityHours`, `validityNotBeforeOffsetMinutes` -- int32
- `signatureAlg` -- string
- `useTokenExpiration` -- boolean
- `cakey`, `cacertificate` -- string

### CertificateSigning
Required: certificateConfig.
- `certificateConfig` -- $ref CertificateConfig
- `microsoftCAConfig` -- $ref MicrosoftCAConfig
- `ca` -- enum: MICROSOFT_CA, TAK_SERVER
- `takserverCAConfig` -- $ref TAKServerCAConfig

### Whitelist
- `groups` -- string array
- `domain`, `token` -- string
- `privateGroup` -- boolean
- `group` -- string

---

## Mission & Content Models

### Mission
- `name`, `creatorUid` -- string
- `createTime` -- date-time
- `keywords` -- string array
- `uids` -- array of UID references (data, timestamp, creatorUid)
- `contents` -- array of content references (data with hash/mimeType/name/size/etc, timestamp, creatorUid)

### MissionChange
- `type` -- string (CREATE_MISSION, ADD_CONTENT, etc.)
- `contentResource` -- $ref Resource (for file additions)
- `contentUid` -- string (for CoT UID additions)
- `missionName` -- string
- `timestamp` -- date-time
- `creatorUid` -- string

### MissionContent
- `hashes` -- string array
- `uids` -- string array

### MissionLayer
- type enum: GROUP, UID, CONTENTS, MAPLAYER, ITEM

### MissionRole
- `type` -- enum: MISSION_OWNER, MISSION_SUBSCRIBER, MISSION_READONLY_SUBSCRIBER

### MissionInvitation
- `missionName`, `invitee`, `type`, `creatorUid` -- string
- `createTime` -- date-time
- `token` -- string
- `role` -- $ref MissionRole
- `missionGuid` -- uuid

### MissionFeed
- Mission-associated data feed configuration

### MapLayer
- Map layer associated with a mission

### Resource
Enterprise sync resource metadata.
- `keywords` -- string array
- `mimeType`, `name` -- string
- `submissionTime` -- date-time
- `submitter`, `uid`, `creatorUid` -- string
- `hash` -- string
- `size` -- integer

### ExternalMissionData
External data reference associated with a mission.

### LogEntry
Mission log entry (Recce Log).

---

## Federation Models

### Federation
- `federationServer` -- $ref FederationServer
- `federationOutgoing` -- array of $ref FederationOutgoing
- `missionDisruptionTolerance` -- $ref MissionDisruptionTolerance
- `federate` -- array of $ref Federate
- `fileFilter` -- $ref FileFilter (required)
- `federateCA` -- array of $ref FederateCA
- `allowDuplicate`, `allowFederatedDelete`, `allowMissionFederation` -- boolean
- `allowDataFeedFederation`, `enableMissionFederationDisruptionTolerance` -- boolean
- `federateOnlyPublicMissions`, `enableFederation` -- boolean
- `enableDataPackageAndMissionFileFilter` -- boolean
- `missionFederationDisruptionToleranceRecencySeconds` -- int64

### FederationServer
Required: federationTokenAuthentication, tls.
- `tls` -- $ref Tls
- `federationPort` -- array of $ref FederationPort
- `v1Tls` -- array of $ref V1Tls
- `federationTokenAuthentication` -- $ref FederationTokenAuthentication
- `port`, `coreVersion` -- int32
- `v1Enabled` -- boolean
- `v2Port` -- int32
- `v2Enabled` -- boolean
- `webBaseUrl` -- string
- `httpsPort` -- int32
- `healthCheckIntervalSeconds`, `initializationDelaySeconds` -- int32
- `maxMessageSizeBytes` -- int32

### FederationOutgoing
Outgoing federation connection configuration.

### FederateCA
- `inboundGroup`, `outboundGroup` -- string array
- `fingerprint` -- string
- `maxHops` -- int32
- `allowTokenAuth` -- boolean
- `tokenAuthDuration` -- int64

### Federate
Federation peer configuration.

### RemoteContact
- `contactName` -- string
- `lastHeardFromMillis` -- int64
- `endpoint`, `uid` -- string
- `groupMappings` -- array of $ref GroupMapping
- `outGroups`, `inGroups` -- string array

### GroupMapping
- `remoteSourceGroup`, `localInboundGroup` -- string

### CertificateSummary
- `issuerDN`, `subjectDN` -- string
- `serialNumber` -- integer
- `fingerPrint` -- string
- `maxHops` -- int32
- `allowTokenAuth` -- boolean
- `tokenAuthDuration` -- int64

### OutboundGroupHopLimit
Federate outbound group hop limit configuration.

### FileFilter
- `fileExtension` -- string array

---

## Subscription & Contact Models

### ClientEndpoint
- `callsign`, `uid`, `username` -- string
- `lastEventTime` -- date-time
- `lastStatus` -- string

### RemoteSubscription
Full subscription info for a connected client.

### RemoteSubscriptionLite
- `filterGroups` -- string array
- `notes`, `callsign`, `team`, `role`, `takv`, `uid` -- string

### RemoteSubscriptionLiteWithUser
Same as RemoteSubscriptionLite plus:
- `user` -- $ref User

### SubscriptionInfo
Subscription metadata including metrics.

### RemoteSubscriptionMetrics
Subscription performance metrics.

### MissionSubscription
Mission-specific subscription.

---

## Filter & QoS Models

### Filter
XML filter configuration.

### Flowtag
Flow tag filter.

### GeospatialFilter
Geospatial area filter.

### Dropfilter
Drop filter configuration.

### Injectionfilter
Injection filter.

### Typefilter
CoT type filter.

### Scrubber
Data scrubber configuration.

### Qos
Quality of Service configuration.

### RateLimitRule
Rate limiting rule.

### ReadRateLimiter / DeliveryRateLimiter / DosRateLimiter
Specific rate limiter types.

---

## Certificate Models

### TakCert
- `id` -- int64
- `creatorDn`, `subjectDn`, `userDn` -- string
- `certificate`, `hash` -- string
- `clientUid` -- string
- `issuanceDate`, `expirationDate`, `effectiveDate`, `revocationDate` -- date-time
- `token`, `serialNumber` -- string

---

## API Response Wrappers

All responses follow the envelope: `{version: string, type: string, data: <T>, messages: string[], nodeId: string}`

### Mission Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseSetMission | Mission[] (uniqueItems) |
| ApiResponseListMission | Mission[] |
| ApiResponseMission | Mission |
| ApiResponseMissionSubscription | MissionSubscription |
| ApiResponseListMissionSubscription | MissionSubscription[] |
| ApiResponseSetMissionChange | MissionChange[] (uniqueItems) |
| ApiResponseListMissionChange | MissionChange[] |
| ApiResponseMissionLayer | MissionLayer |
| ApiResponseListMissionLayer | MissionLayer[] |
| ApiResponseMissionRole | MissionRole |
| ApiResponseListLogEntry | LogEntry[] |
| ApiResponseListMissionInvitation | MissionInvitation[] |
| ApiResponseSetMissionInvitation | MissionInvitation[] (uniqueItems) |
| ApiResponseExternalMissionData | ExternalMissionData |
| ApiResponseNavigableSetResource | Resource[] |
| ApiResponseListResource | Resource[] |

### Map & Content Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseMapLayer | MapLayer |
| ApiResponseCollectionMapLayer | MapLayer[] |
| ApiResponseSetCopHierarchyNode | CopHierarchyNode[] (uniqueItems) |

### Client & Contact Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseListClientEndpoint | ClientEndpoint[] |
| ApiResponseSetSubscriptionInfo | SubscriptionInfo[] (uniqueItems) |
| ApiResponseSubscriptionInfo | SubscriptionInfo |

### Certificate Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseListTakCert | TakCert[] |
| ApiResponseTakCert | TakCert |

### Federation Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseFederationConfigInfo | FederationConfigInfo |
| ApiResponseFederateOutgoing | FederateOutgoing |
| ApiResponseFederateMissionPerConnectionSettings | FederateMissionPerConnectionSettings |
| ApiResponseSortedSetOutgoingConnectionSummary | OutgoingConnectionSummary[] |
| ApiResponseListConnectionInfoSummary | ConnectionInfoSummary[] |
| ApiResponseConnectionStatus | ConnectionStatus |
| ApiResponseSortedSetFederate | Federate[] |
| ApiResponseListFederateGroupAssociation | FederateGroupAssociation[] |
| ApiResponseSortedSetRemoteContact | RemoteContact[] |
| ApiResponseListCertificateSummary | CertificateSummary[] |
| ApiResponseListFederateCAGroupAssociation | FederateCAGroupAssociation[] |
| ApiResponseListOutboundGroupHopLimit | OutboundGroupHopLimit[] |
| ApiResponseJwtTokenResponseModel | JwtTokenResponseModel |
| ApiResponseBoolean | boolean |

### Config & Admin Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseAuthenticationConfigInfo | AuthenticationConfigInfo |
| ApiResponseSecurityConfigInfo | SecurityConfigInfo |
| ApiResponseMessagingConfigInfo | MessagingConfigInfo |
| ApiResponseServerConfig | ServerConfig |
| ApiResponseProfile | Profile |
| ApiResponseListProfile | Profile[] |
| ApiResponseProfileFile | ProfileFile |
| ApiResponseListProfileFile | ProfileFile[] |
| ApiResponseListProfileDirectory | ProfileDirectory[] |

### Data Feed Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseDataFeed | DataFeed |
| ApiResponseListDataFeed | DataFeed[] |
| ApiResponsePredicateDataFeed | PredicateDataFeed |
| ApiResponseDataFeedStats | DataFeedStats |
| ApiResponseListDataFeedStats | DataFeedStats[] |

### Input Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseInput | Input |
| ApiResponseSortedSetInputMetric | InputMetric[] (uniqueItems) |
| ApiResponseConnectionModifyResult | ConnectionModifyResult |

### Generic Responses
| Wrapper | Data Type |
|---------|-----------|
| ApiResponseString | string |
| ApiResponseInteger | integer |
| ApiResponseBoolean | boolean |
| ApiResponseListString | string[] |
| ApiResponseSetString | string[] (uniqueItems) |
| ApiResponseMapStringString | Map<String, String> |
| ApiResponseMapStringCollectionString | Map<String, String[]> |
| ApiResponseCollectionString | string[] |
| ApiResponseListTokenResult | TokenResult[] |
| ApiResponseListCotSearch | CotSearch[] |
| ApiResponseListRepeatable | Repeatable[] |
| ApiResponseCollectionPluginInfo | PluginInfo[] |
| ApiResponseSetInjectorConfig | InjectorConfig[] (uniqueItems) |
| ApiResponseUserGroups | UserGroups |
| ApiResponseSortedSetUser | User[] (uniqueItems) |
| ApiResponseEntryIntegerInteger | Entry<Integer, Integer> |
| ApiResponseQos | Qos |

---

## Other Models

### DataFeed
Data feed configuration.

### DataFeedStats
- `dataFeedUUID` -- string
- `dataFeedCotTypes` -- string[] (uniqueItems)
- `dataFeedMinLat`, `dataFeedMinLon`, `dataFeedMaxLat`, `dataFeedMaxLon` -- double
- `dataFeedMsgMillis`, `dataFeedNumMessages`, `dataFeedFirstMsgMillis`, `dataFeedTotalByteSize` -- int64
- `dataFeedMsgAvgRatePerSec`, `dataFeedMsgAvgBytesPerSec` -- float
- `dataFeedExtents` -- string

### PredicateDataFeed
Predicate-based data feed.

### Input
Server input configuration.

### InputMetric
- `id` -- string
- `input` -- $ref Input
- `readsReceived`, `messagesReceived`, `numClients`, `bytesRecieved` -- counter objects (with opaque, acquire, release, andIncrement, andDecrement, plain -- all int64)

### VideoConnection
Video feed connection configuration.

### VideoCollections
Collection of video connections.

### InjectorConfig
CoT injector configuration.

### PluginInfo
Plugin metadata and status.

### Repeatable
Repeater configuration entry.

### Profile / ProfileFile / ProfileDirectory
Device profile and associated files/directories.

### CopHierarchyNode
- `name` -- string

### TokenResult
Token information.

### VersionInfo
Server version metadata.

### ServerConfig
Server configuration summary.

### CotSearch
CoT search parameters.

### VBMConfigurationModel
VBM configuration.

### FileConfigurationModel
File storage configuration.

### User / UserGroups / TAKUser
User account models.

### GroupNameModel / UsernameModel
Simple name models for group and user references.

### JwtTokenRequestModel / JwtTokenResponseModel
JWT token request/response for federation.

### FederateGroupAssociation / FederateCAHopsAssociation / FederateCAGroupAssociation
Federation group mapping models.

### SimpleUserGroupModel / SimpleGroupWithUsersModel / UserPasswordModel / NewUserModel / UserGenerationInBulkModel
User management request models.
