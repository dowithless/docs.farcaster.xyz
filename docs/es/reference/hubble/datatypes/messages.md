# Mensajes

Un mensaje es el tipo de dato fundamental en la red Farcaster.

Cuando una cuenta realiza una acción como publicar un mensaje público, cambiar su perfil o verificar una cuenta de Ethereum, genera nuevos mensajes.

## 1. Mensaje

El mensaje es un protobuf que contiene los datos, su hash y una firma del autor.

| Campo            | Tipo                                | Etiqueta | Descripción                                                                                                                                                                                                                                                     |
| ---------------- | ----------------------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| data             | [MessageData](#MessageData)         |          | Contenido del mensaje. Alternativamente, puedes usar data_bytes para serializar el `MessageData`                                                                                                                                                                |
| hash             | bytes                               |          | Digestión hash de los datos                                                                                                                                                                                                                                     |
| hash_scheme      | [HashScheme](#HashScheme)           |          | Esquema hash que produjo la digestión hash                                                                                                                                                                                                                      |
| signature        | bytes                               |          | Firma de la digestión hash                                                                                                                                                                                                                                      |
| signature_scheme | [SignatureScheme](#SignatureScheme) |          | Esquema de firma que produjo la firma                                                                                                                                                                                                                           |
| signer           | bytes                               |          | Clave pública o dirección del par de claves que produjo la firma                                                                                                                                                                                                |
| data_bytes       | bytes                               |          | Alternativa al campo "data". Si estás construyendo el [MessageData](#MessageData) en un lenguaje de programación distinto a Typescript, puedes usar este campo para serializar el `MessageData` y calcular el `hash` y `signature` sobre estos bytes. Opcional. |

### 1.1 MessageData

MessageData es un envoltorio genérico que contiene un tipo, fid, timestamp y red que deben estar presentes en todos los mensajes de Farcaster. También contiene un cuerpo cuyo tipo está determinado por el MessageType.

| Campo     | Tipo                                                                                                                                                                                                                                                                                                                                    | Etiqueta | Descripción                                      |
| --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------------------------------------------------ |
| type      | [MessageType](#MessageType)                                                                                                                                                                                                                                                                                                             |          | Tipo de Mensaje contenido en el cuerpo           |
| fid       | uint64                                                                                                                                                                                                                                                                                                                                  |          | Farcaster ID del usuario que produce el mensaje  |
| timestamp | uint32                                                                                                                                                                                                                                                                                                                                  |          | Timestamp de época Farcaster en segundos         |
| network   | [FarcasterNetwork](#FarcasterNetwork)                                                                                                                                                                                                                                                                                                   |          | Red Farcaster a la que está destinado el mensaje |
| body      | [CastAddBody](#CastAddBody), <br> [CastRemoveBody](#CastRemoveBody), <br> [ReactionBody](#ReactionBody), <br>[VerificationAddEthAddressBody](#VerificationAddEthAddressBody), <br>[VerificationRemoveBody](#VerificationRemoveBody), <br> [UserDataBody](#UserDataBody),<br> [LinkBody](#LinkBody),<br> [UserNameProof](#UserNameProof) | oneOf    | Propiedades específicas del MessageType          |

### 1.2 HashScheme

Tipo de esquema hash utilizado para producir una digestión de MessageData

| Nombre             | Número | Descripción                                     |
| ------------------ | ------ | ----------------------------------------------- |
| HASH_SCHEME_NONE   | 0      |                                                 |
| HASH_SCHEME_BLAKE3 | 1      | Esquema predeterminado para hashear MessageData |

### 1.3 Signature Scheme

Tipo de esquema de firma utilizado para firmar el hash del Mensaje

| Nombre                   | Número | Descripción                           |
| ------------------------ | ------ | ------------------------------------- |
| SIGNATURE_SCHEME_NONE    | 0      |                                       |
| SIGNATURE_SCHEME_ED25519 | 1      | Firma Ed25519 (predeterminado)        |
| SIGNATURE_SCHEME_EIP712  | 2      | Firma ECDSA usando el esquema EIP-712 |

### 1.4 Message Type

Tipo del MessageBody

| Nombre                                    | Número | Descripción                                         |
| ----------------------------------------- | ------ | --------------------------------------------------- |
| MESSAGE_TYPE_NONE                         | 0      | Valor predeterminado inválido                       |
| MESSAGE_TYPE_CAST_ADD                     | 1      | Añadir un nuevo Cast                                |
| MESSAGE_TYPE_CAST_REMOVE                  | 2      | Eliminar un Cast existente                          |
| MESSAGE_TYPE_REACTION_ADD                 | 3      | Añadir una Reacción a un Cast                       |
| MESSAGE_TYPE_REACTION_REMOVE              | 4      | Eliminar una Reacción de un Cast                    |
| MESSAGE_TYPE_LINK_ADD                     | 5      | Añadir un Link a un objetivo                        |
| MESSAGE_TYPE_LINK_REMOVE                  | 6      | Eliminar un Link de un objetivo                     |
| MESSAGE_TYPE_VERIFICATION_ADD_ETH_ADDRESS | 7      | Añadir una Verificación de una Dirección Ethereum   |
| MESSAGE_TYPE_VERIFICATION_REMOVE          | 8      | Eliminar una Verificación                           |
| MESSAGE_TYPE_USER_DATA_ADD                | 11     | Añadir metadatos sobre un usuario                   |
| MESSAGE_TYPE_USERNAME_PROOF               | 12     | Añadir o reemplazar una prueba de nombre de usuario |

### 1.5 Farcaster Network

Red Farcaster a la que está destinado el mensaje

| Nombre                    | Número | Descripción           |
| ------------------------- | ------ | --------------------- |
| FARCASTER_NETWORK_NONE    | 0      |                       |
| FARCASTER_NETWORK_MAINNET | 1      | Red principal pública |
| FARCASTER_NETWORK_TESTNET | 2      | Red de prueba pública |
| FARCASTER_NETWORK_DEVNET  | 3      | Red de prueba privada |

## 2. UserData

Un mensaje UserData representa metadatos de usuario (por ejemplo, una URL de foto de perfil).

### 2.1 UserDataBody

Cuerpo de un mensaje UserData

| Campo | Tipo                          | Etiqueta | Descripción            |
| ----- | ----------------------------- | -------- | ---------------------- |
| type  | [UserDataType](#UserDataType) |          | Tipo de metadatos      |
| value | string                        |          | Valor de los metadatos |

### 2.2 UserDataType

Tipo de mensaje UserData

| Nombre                  | Número | Descripción                              |
| ----------------------- | ------ | ---------------------------------------- |
| USER_DATA_TYPE_NONE     | 0      | Valor predeterminado inválido            |
| USER_DATA_TYPE_PFP      | 1      | Foto de perfil del usuario               |
| USER_DATA_TYPE_DISPLAY  | 2      | Nombre para mostrar del usuario          |
| USER_DATA_TYPE_BIO      | 3      | Biografía del usuario                    |
| USER_DATA_TYPE_URL      | 5      | URL del usuario                          |
| USER_DATA_TYPE_USERNAME | 6      | Nombre Farcaster preferido del usuario   |
| USER_DATA_TYPE_LOCATION | 7      | Ubicación del usuario                    |
| USER_DATA_TYPE_TWITTER  | 8      | Nombre de usuario de Twitter del usuario |
| USER_DATA_TYPE_GITHUB   | 9      | Nombre de usuario de GitHub del usuario  |

Consulta [FIP-196](https://github.com/farcasterxyz/protocol/discussions/196) para más información sobre Ubicación.
Consulta [FIP-19](https://github.com/farcasterxyz/protocol/discussions/199) para más información sobre nombres de usuario de Twitter/X y Github.

## 3. Cast

Un mensaje Cast es una publicación pública de un usuario.

### 3.1 CastAddBody

Añade un nuevo mensaje Cast.

| Campo              | Tipo              | Etiqueta | Descripción                                  |
| ------------------ | ----------------- | -------- | -------------------------------------------- |
| embeds_deprecated  | string            | repeated | URLs para incrustar en el cast               |
| mentions           | uint64            | repeated | Fids mencionados en el cast                  |
| parent_cast_id     | [CastId](#CastId) |          | Cast padre del cast                          |
| parent_url         | string            |          | URL padre del cast                           |
| text               | string            |          | Texto del cast                               |
| mentions_positions | uint32            | repeated | Posiciones de las menciones en el texto      |
| embeds             | [Embed](#Embed)   | repeated | URLs o ids de cast para incrustar en el cast |

#### Embed

| Campo   | Tipo              | Etiqueta | Descripción |
| ------- | ----------------- | -------- | ----------- |
| url     | [string](#string) |          |             |
| cast_id | [CastId](#CastId) |          |             |

### 3.2 CastRemoveBody

Elimina un mensaje Cast existente.

| Campo       | Tipo  | Etiqueta | Descripción              |
| ----------- | ----- | -------- | ------------------------ |
| target_hash | bytes |          | Hash del cast a eliminar |

### 3.3 CastId

Identificador utilizado para buscar un Cast

| Campo | Tipo   | Etiqueta | Descripción                      |
| ----- | ------ | -------- | -------------------------------- |
| fid   | uint64 |          | Fid del usuario que creó el cast |
| hash  | bytes  |          | Hash del cast                    |

## 4. Reacción

Un mensaje Reacción crea una relación entre una cuenta y un cast (por ejemplo, un "me gusta").

### 4.1 ReactionBody

Añade o elimina una Reacción de un Cast

| Campo          | Tipo                          | Etiqueta | Descripción                       |
| -------------- | ----------------------------- | -------- | --------------------------------- |
| type           | [ReactionType](#ReactionType) |          | Tipo de reacción                  |
| target_cast_id | [CastId](#CastId)             |          | CastId del Cast al que reaccionar |
| target_url     | [string](#string)             |          | URL a la que reaccionar           |

### 4.2 ReactionType

Tipo de Reacción

| Nombre               | Número | Descripción                                             |
| -------------------- | ------ | ------------------------------------------------------- |
| REACTION_TYPE_NONE   | 0      | Valor predeterminado inválido                           |
| REACTION_TYPE_LIKE   | 1      | "Me gusta" el cast objetivo                             |
| REACTION_TYPE_RECAST | 2      | Compartir el cast objetivo con la audiencia del usuario |

## 5. Link

Un mensaje Link crea una relación entre dos usuarios (por ejemplo, seguir).

### 5.1 LinkBody

Añade o elimina un Link

| Campo            | Tipo              | Etiqueta | Descripción                                                                                                                                |
| ---------------- | ----------------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| type             | [string](#string) |          | Tipo de link, <= 8 caracteres                                                                                                              |
| displayTimestamp | [uint32](#uint32) | opcional | Timestamp definido por el usuario que preserva el timestamp original cuando message.data.timestamp necesita actualizarse para compactación |
| target_fid       | [uint64](#uint64) |          | El fid al que se relaciona el link                                                                                                         |

## 6. Verificación

Un mensaje Verificación es una prueba de propiedad de algo.

### 6.1 VerificationAddEthAddressBody

Añade una firma bidireccional que prueba que un fid tiene control sobre una dirección Ethereum.

| Campo         | Tipo  | Etiqueta | Descripción                                                      |
| ------------- | ----- | -------- | ---------------------------------------------------------------- |
| address       | bytes |          | Dirección Ethereum que se verifica                               |
| eth_signature | bytes |          | Firma producida por la dirección Ethereum del usuario            |
| block_hash    | bytes |          | Hash del último bloque Ethereum cuando se produjo la reclamación |

### 6.2 VerificationRemoveBody

Elimina una Verificación de cualquier tipo

| Campo   | Tipo  | Etiqueta | Descripción                             |
| ------- | ----- | -------- | --------------------------------------- |
| address | bytes |          | Dirección de la Verificación a eliminar |

## 7. Frame Action

Representa una acción realizada por un usuario en un frame. Este mensaje no se almacena en los hubs y no se puede enviar, solo validar.

### 7.1 FrameActionBody

Una acción de usuario en un frame

| Campo        | Tipo              | Etiqueta | Descripción                                                    |
| ------------ | ----------------- | -------- | -------------------------------------------------------------- |
| url          | bytes             |          | La URL original del frame como se incrustó en el cast          |
| button_index | uint32            |          | El botón que se presionó (indexado desde 1)                    |
| cast_id      | [CastId](#CastId) |          | El cast id que alojó el frame                                  |
| input_text   | bytes             |          | Cualquier texto que el usuario ingresó como parte de la acción |
| state        | bytes             |          | Estado serializado pasado del frame al servidor                |
