# API de Sincronización

Estas APIs son utilizadas por los Hubs para sincronizar su estado con otros Hubs. No están destinadas para uso por aplicaciones externas.

## API

| Nombre del Método       | Tipo de Solicitud | Tipo de Respuesta        | Descripción                                                          |
| ----------------------- | ----------------- | ------------------------ | -------------------------------------------------------------------- |
| GetInfo                 | HubInfoRequest    | HubInfoResponse          | Devuelve metadatos sobre el estado del hub.                          |
| GetSyncStatus           | SyncStatusRequest | SyncStatusResponse       | Devuelve el estado de sincronización del hub.                        |
| GetAllSyncIdsByPrefix   | TrieNodePrefix    | SyncIds                  | Obtiene todos los SyncIds para un prefijo específico                 |
| GetAllMessagesBySyncIds | SyncIds           | MessagesResponse         | Obtiene todos los mensajes dados los sync ids correspondientes       |
| GetSyncMetadataByPrefix | TrieNodePrefix    | TrieNodeMetadataResponse | Obtiene metadatos de sincronización para un prefijo específico       |
| GetSyncSnapshotByPrefix | TrieNodePrefix    | TrieNodeSnapshotResponse | Obtiene una instantánea de sincronización para un prefijo específico |

## HubInfoRequest

| Campo    | Tipo          | Etiqueta | Descripción |
| -------- | ------------- | -------- | ----------- |
| db_stats | [bool](#bool) |          |             |

## HubInfoResponse

Tipos de Respuesta para los Métodos RPC de Sincronización

| Campo      | Tipo                | Etiqueta | Descripción |
| ---------- | ------------------- | -------- | ----------- |
| version    | [string](#string)   |          |             |
| is_syncing | [bool](#bool)       |          |             |
| nickname   | [string](#string)   |          |             |
| root_hash  | [string](#string)   |          |             |
| db_stats   | [DbStats](#DbStats) |          |             |

## SyncStatusRequest

| Campo  | Tipo              | Etiqueta | Descripción |
| ------ | ----------------- | -------- | ----------- |
| peerId | [string](#string) | opcional |             |

## SyncStatusResponse

| Campo       | Tipo                      | Etiqueta | Descripción |
| ----------- | ------------------------- | -------- | ----------- |
| is_syncing  | [bool](#bool)             |          |             |
| sync_status | [SyncStatus](#SyncStatus) | repetido |             |

## SyncStatus

| Campo                | Tipo              | Etiqueta | Descripción |
| -------------------- | ----------------- | -------- | ----------- |
| peerId               | [string](#string) |          |             |
| inSync               | [string](#string) |          |             |
| shouldSync           | [bool](#bool)     |          |             |
| divergencePrefix     | [string](#string) |          |             |
| divergenceSecondsAgo | [int32](#int32)   |          |             |
| theirMessages        | [uint64](#uint64) |          |             |
| ourMessages          | [uint64](#uint64) |          |             |
| lastBadSync          | [int64](#int64)   |          |             |

## TrieNodePrefix

| Campo  | Tipo            | Etiqueta | Descripción |
| ------ | --------------- | -------- | ----------- |
| prefix | [bytes](#bytes) |          |             |

## SyncIds

| Campo    | Tipo            | Etiqueta | Descripción |
| -------- | --------------- | -------- | ----------- |
| sync_ids | [bytes](#bytes) | repetido |             |

## TrieNodeMetadataResponse

| Campo        | Tipo                                                  | Etiqueta | Descripción |
| ------------ | ----------------------------------------------------- | -------- | ----------- |
| prefix       | [bytes](#bytes)                                       |          |             |
| num_messages | [uint64](#uint64)                                     |          |             |
| hash         | [string](#string)                                     |          |             |
| children     | [TrieNodeMetadataResponse](#TrieNodeMetadataResponse) | repetido |             |

## TrieNodeSnapshotResponse

| Campo           | Tipo              | Etiqueta | Descripción |
| --------------- | ----------------- | -------- | ----------- |
| prefix          | [bytes](#bytes)   |          |             |
| excluded_hashes | [string](#string) | repetido |             |
| num_messages    | [uint64](#uint64) |          |             |
| root_hash       | [string](#string) |          |             |

## DbStats

| Campo            | Tipo              | Etiqueta | Descripción |
| ---------------- | ----------------- | -------- | ----------- |
| num_messages     | [uint64](#uint64) |          |             |
| num_fid_events   | [uint64](#uint64) |          |             |
| num_fname_events | [uint64](#uint64) |          |             |
