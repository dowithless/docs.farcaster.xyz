# Eventos

Los eventos representan cambios de estado, como un nuevo mensaje o un evento de contrato.

Hubble emite eventos cada vez que observa un cambio de estado. Dado que un hub puede ver mensajes en un orden diferente al de otros hubs, el orden de los eventos es específico para cada hub. Los clientes pueden suscribirse al hub utilizando la [API de Eventos](/es/reference/hubble/grpcapi/events) para obtener un flujo en vivo de los cambios en el hub.

Hubble conserva los eventos durante 3 días, después de los cuales se eliminan para ahorrar espacio. Para obtener datos más antiguos, utiliza las APIs [GRPC](../grpcapi/grpcapi.md) o [HTTP](../httpapi/httpapi.md).

## HubEvent

| Campo | Tipo                                                                                                                                                                                                                                        | Etiqueta | Descripción |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ----------- |
| type  | [HubEventType](#HubEventType)                                                                                                                                                                                                               |          |             |
| id    | [uint64](#uint64)                                                                                                                                                                                                                           |          |             |
| body  | [MergeMessageBody](#mergemessagebody), <br> [PruneMessageBody](#prunemessagebody), <br> [RevokeMessageBody](#revokemessagebody), <br>[MergeUserNameProofBody](#mergeusernameproofbody), <br>[MergeOnChainEventBody](#mergeonchaineventbody) | oneOf    |             |

## HubEventType

| Nombre                              | Número | Descripción |
| ----------------------------------- | ------ | ----------- |
| HUB_EVENT_TYPE_NONE                 | 0      |             |
| HUB_EVENT_TYPE_MERGE_MESSAGE        | 1      |             |
| HUB_EVENT_TYPE_PRUNE_MESSAGE        | 2      |             |
| HUB_EVENT_TYPE_REVOKE_MESSAGE       | 3      |             |
| HUB_EVENT_TYPE_MERGE_USERNAME_PROOF | 6      |             |
| HUB_EVENT_TYPE_MERGE_ON_CHAIN_EVENT | 9      |             |

<a name="-MergeMessageBody"></a>

## MergeMessageBody

| Campo            | Tipo                | Etiqueta | Descripción |
| ---------------- | ------------------- | -------- | ----------- |
| message          | [Message](#Message) |          |             |
| deleted_messages | [Message](#Message) | repeated |             |

<a name="-MergeUserNameProofBody"></a>

## MergeUserNameProofBody

| Campo                          | Tipo                            | Etiqueta | Descripción |
| ------------------------------ | ------------------------------- | -------- | ----------- |
| username_proof                 | [UserNameProof](#UserNameProof) |          |             |
| deleted_username_proof         | [UserNameProof](#UserNameProof) |          |             |
| username_proof_message         | [Message](#Message)             |          |             |
| deleted_username_proof_message | [Message](#Message)             |          |             |

<a name="-PruneMessageBody"></a>

## PruneMessageBody

| Campo   | Tipo                | Etiqueta | Descripción |
| ------- | ------------------- | -------- | ----------- |
| message | [Message](#Message) |          |             |

<a name="-RevokeMessageBody"></a>

## RevokeMessageBody

| Campo   | Tipo                | Etiqueta | Descripción |
| ------- | ------------------- | -------- | ----------- |
| message | [Message](#Message) |          |             |

<a name="-MergeOnChainEventBody"></a>

## MergeOnChainEventBody

| Campo          | Tipo                          | Etiqueta | Descripción |
| -------------- | ----------------------------- | -------- | ----------- |
| on_chain_event | [OnChainEvent](#OnChainEvent) |          |             |

<a name="-HubEventType"></a>

## OnChainEvent

| Campo            | Tipo                                                                                                                                                                                               | Etiqueta | Descripción                                |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------------------------------------------ |
| type             | [OnChainEventType](#OnChainEventType)                                                                                                                                                              |          | El tipo de evento onchain                  |
| chain_id         | [uint32](#)                                                                                                                                                                                        |          | El ID de la cadena para el evento          |
| block_number     | [uint32](#)                                                                                                                                                                                        |          | El número de bloque para el evento         |
| block_hash       | [bytes](#)                                                                                                                                                                                         |          | El hash del bloque para el evento          |
| block_timestamp  | [uint64](#)                                                                                                                                                                                        |          | El timestamp del bloque para el evento     |
| transaction_hash | [bytes](#)                                                                                                                                                                                         |          | El hash de la transacción para el evento   |
| log_index        | [uint32](#)                                                                                                                                                                                        |          | El índice del log para el evento           |
| fid              | [uint64](#)                                                                                                                                                                                        |          | El fid asociado al evento                  |
| body             | [SignerEventBody](#signereventbody), <br> [SignerMigratedEventBody](#signermigratedeventbody), <br> [IdRegisterEventBody](#idregistereventbody), <br>[StorageRentEventBody](#storagerenteventbody) | oneOf    |                                            |
| tx_index         | [uint32](#)                                                                                                                                                                                        |          | El índice de la transacción para el evento |

<a name="-OnChainEventType"></a>

## OnChainEventType

| Nombre                     | Número | Descripción |
| -------------------------- | ------ | ----------- |
| EVENT_TYPE_NONE            | 0      |             |
| EVENT_TYPE_SIGNER          | 1      |             |
| EVENT_TYPE_SIGNER_MIGRATED | 2      |             |
| EVENT_TYPE_ID_REGISTER     | 3      |             |
| EVENT_TYPE_STORAGE_RENT    | 4      |             |

<a name="-SignerEventBody"></a>

## SignerEventBody

| Campo         | Tipo                                | Etiqueta | Descripción                                               |
| ------------- | ----------------------------------- | -------- | --------------------------------------------------------- |
| key           | [bytes](#)                          |          | Los bytes de la clave pública del firmante                |
| key_type      | [uint32](#)                         |          | El tipo de clave (actualmente solo se establece en 1)     |
| event_type    | [SignerEventType](#SignerEventType) |          | El tipo de evento del firmante                            |
| metadata      | [bytes](#)                          |          | Los metadatos asociados con la clave                      |
| metadata_type | [uint32](#)                         |          | El tipo de metadatos (actualmente solo se establece en 1) |

<a name="-SignerEventType"></a>

## SignerEventType

| Nombre                        | Número | Descripción |
| ----------------------------- | ------ | ----------- |
| SIGNER_EVENT_TYPE_NONE        | 0      |             |
| SIGNER_EVENT_TYPE_ADD         | 1      |             |
| SIGNER_EVENT_TYPE_REMOVE      | 2      |             |
| SIGNER_EVENT_TYPE_ADMIN_RESET | 3      |             |

<a name="-SignerMigratedEventBody"></a>

## SignerMigratedEventBody

| Campo       | Tipo        | Etiqueta | Descripción                                           |
| ----------- | ----------- | -------- | ----------------------------------------------------- |
| migrated_at | [uint32](#) |          | El timestamp en el que los hubs migraron a OP mainnet |

<a name="-SignerEventBody"></a>

## SignerEventBody

| Campo           | Tipo                                        | Etiqueta | Descripción                                           |
| --------------- | ------------------------------------------- | -------- | ----------------------------------------------------- |
| to              | [bytes](#)                                  |          | La dirección a la que se registró/transfirió el fid   |
| event_type      | [IdRegisterEventType](#IdRegisterEventType) |          | El tipo de evento de registro de ID                   |
| from            | [bytes](#)                                  |          | La dirección desde la que se originó la transferencia |
| recover_address | [bytes](#)                                  |          | La dirección de recuperación para el fid              |

<a name="-IdRegisterEventType"></a>

## IdRegisterEventType

| Nombre                                 | Número | Descripción |
| -------------------------------------- | ------ | ----------- |
| ID_REGISTER_EVENT_TYPE_NONE            | 0      |             |
| ID_REGISTER_EVENT_TYPE_REGISTER        | 0      |             |
| ID_REGISTER_EVENT_TYPE_TRANSFER        | 0      |             |
| ID_REGISTER_EVENT_TYPE_CHANGE_RECOVERY | 0      |             |

<a name="-StorageRentEventBody"></a>

## StorageRentEventBody

| Campo  | Tipo        | Etiqueta | Descripción                                                       |
| ------ | ----------- | -------- | ----------------------------------------------------------------- |
| payer  | [bytes](#)  |          | La dirección del pagador                                          |
| units  | [uint32](#) |          | El número de unidades de almacenamiento adquiridas                |
| expiry | [uint32](#) |          | El timestamp en el que expirarán estas unidades de almacenamiento |
