# TAK Server Configuration Schemas

Server configuration schemas use XML namespace `http://bbn.com/marti/xml/config`.

## Configuration Objects

### Security
- `tls` -- TLS configuration ($ref Tls)
- `missionTls` -- array of MissionTls configs

### Tls
- `keystore`, `keystoreFile`, `keystorePass` -- server key material
- `truststore`, `truststoreFile`, `truststorePass` -- trusted CAs
- `context` -- TLS context string
- `ciphers` -- allowed cipher suites
- `keymanager` -- key manager type
- `enableOCSP` -- boolean, OCSP stapling

### Repository
- `connection` -- database connection ($ref Connection, required)
- `enable` -- boolean
- `numDbConnections` -- int32
- `connectionPoolAutoSize` -- boolean
- `primaryKeyBatchSize` -- int32
- `insertionBatchSize` -- int32
- `archive` -- boolean
- `iconsetDir` -- string
- `enableCallsignAudit` -- boolean
- `contactCacheMaxClearRateSeconds` -- int32
- `dbTimeoutMs` -- int64
- `dbConnectionMaxLifetimeMs` -- int32
- `dbConnectionMaxIdleMs` -- int32
- `poolScaleFactor` -- int32

### Queue
- `priority` -- Priority object (levels: int32)
- `pubSubCapacity`, `outboundCapacity`, `inboundCapacity` -- int32
- `codecWrapperCapacity`, `tcpWriteQueueCapacity` -- int32
- `disconnectOnFull` -- boolean
- `maxWriteQueueSize`, `defaultExecQueueSize` -- int32
- `defaultMaxPoolSize`, `defaultMaxPoolFactor`, `defaultCoreMaxPoolFactor` -- int32
- `messageWriteQueueSize`, `messageWriteExecutorQueueSize` -- int32
- `codecViewPendingCapacity` -- int32
- `queueSizeInitial`, `queueSizeIncrement`, `queueSizeMaxCapacity` -- int32
- `coreExecutorCapacity` -- int32
- `throwOnAssertionFail`, `disconnectOnPendingExceeded` -- boolean
- `flushInterval` -- int32
- `websocketSendBufferSizeLimit`, `websocketMaxBinaryMessageBufferSize` -- int32
- `websocketMaxSessionIdleTimeout` -- int64
- `websocketSendTimeoutMs` -- int32
- `missionUidLimit`, `missionContentLimit` -- int32
- `missionConcurrentDownloadLimit` -- int32
- `nearCacheMaxSize`, `cotCacheMaxSize`, `cotCacheBatchSize` -- int32
- `cotCacheMaxMemorySize` -- int32
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

### Vbm (Virtual Battle Map)
- `enabled` -- boolean
- `disableSASharing`, `disableChatSharing` -- boolean
- `returnCopsWithPublicMissions` -- boolean
- `ismUrl` -- string
- `ismConnectTimeoutSeconds`, `ismReadTimeoutSeconds` -- int64
- `ismStrictEnforcing` -- boolean

### Subscription
- `reloadPersistent` -- boolean
- `static` -- array of Static objects

### Static (subscription input)
- `filtergroup` -- string array
- `filter` -- Filter object
- `name`, `protocol`, `address` -- string
- `port` -- int32
- `xpath` -- string
- `federated`, `iface` -- string/boolean

### Submission
- `ignoreStaleMessages` -- boolean
- `validateXml` -- boolean
- `dropMessagesIfAnyServiceIsFull` -- boolean

### Repeater
- `repeatableType` -- array of RepeatableType (name, initiateTest, cancelTest)
- `enable` -- boolean
- `periodMillis`, `staleDelayMillis` -- int32
- `maxAllowedRepeatables` -- int32

### Plugins
- `usePluginMessageQueue` -- boolean
- `allowPluginContacts` -- boolean
- `pluginContactCleanupFrequencySeconds` -- int32

### Whitelist
- `groups` -- string array
- `domain`, `token` -- string
- `privateGroup` -- boolean
- `group` -- string

### Xmpp
- `xmppHost`, `takServerHost` -- string
- `xmppPort`, `takServerPort` -- int32
- `xmppSharedSecret` -- string
- `xmppComponentRetryCount`, `xmppComponentRetryDelay` -- int32

### TAKServerCAConfig
- `keystore`, `keystoreFile`, `keystorePass` -- string
- `validityDays`, `validityHours` -- int32
- `validityNotBeforeOffsetMinutes` -- int32
- `signatureAlg` -- string
- `useTokenExpiration` -- boolean
- `cakey`, `cacertificate` -- string

### OAuth
- `readOnlyGroup`, `readGroupSuffix`, `writeGroupSuffix` -- string
- `groupsClaim`, `usernameClaim`, `scopeClaim` -- string
- `webtakScope`, `groupprefix` -- string
- `allowUriQueryParameter`, `allowAccessTokenRetrieval` -- boolean

## API Response Wrappers

All API responses follow the envelope pattern `{version, type, data, messages, nodeId}`. Common wrapper types:

| Wrapper | Data Type |
|---------|-----------|
| ApiResponseSetMission | Mission[] |
| ApiResponseMissionSubscription | MissionSubscription |
| ApiResponseListMissionSubscription | MissionSubscription[] |
| ApiResponseSetMissionChange | MissionChange[] |
| ApiResponseListLogEntry | LogEntry[] |
| ApiResponseMissionLayer | MissionLayer |
| ApiResponseListMissionLayer | MissionLayer[] |
| ApiResponseMapLayer | MapLayer |
| ApiResponseCollectionMapLayer | MapLayer[] |
| ApiResponseListMissionInvitation | MissionInvitation[] |
| ApiResponseSetMissionInvitation | MissionInvitation set |
| ApiResponseMissionRole | MissionRole |
| ApiResponseListClientEndpoint | ClientEndpoint[] |
| ApiResponseListTakCert | TakCert[] |
| ApiResponseTakCert | TakCert |
| ApiResponseAuthenticationConfigInfo | AuthenticationConfigInfo |
| ApiResponseListConnectionInfoSummary | ConnectionInfoSummary[] |
| ApiResponseFederationConfigInfo | FederationConfigInfo |
| ApiResponseFederateOutgoing | FederateOutgoing |
| ApiResponseFederateMissionPerConnectionSettings | FederateMissionPerConnectionSettings |
| ApiResponseDataFeed | DataFeed |
| ApiResponsePredicateDataFeed | PredicateDataFeed |
| ApiResponseInput | Input |
| ApiResponseInputMetric | InputMetric |
| ApiResponseMessagingConfigInfo | MessagingConfigInfo |
| ApiResponseConnectionModifyResult | ConnectionModifyResult |
| ApiResponseProfile | Profile |
| ApiResponseProfileFile | ProfileFile |
| ApiResponseString | string |
| ApiResponseInteger | integer |
| ApiResponseBoolean | boolean |
| ApiResponseListString | string[] |

## Key Data Types

### ClientEndpoint
- `callsign`, `uid`, `username` -- string
- `lastEventTime` -- date-time
- `lastStatus` -- string

### TakCert
- `id` -- int64
- `creatorDn`, `subjectDn`, `userDn` -- string
- `certificate`, `hash` -- string
- `clientUid` -- string
- `issuanceDate`, `expirationDate`, `effectiveDate`, `revocationDate` -- date-time
- `token`, `serialNumber` -- string

### Mission Layer Types
Enum: `GROUP`, `UID`, `CONTENTS`, `MAPLAYER`, `ITEM`
