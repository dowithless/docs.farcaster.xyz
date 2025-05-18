# API de OnChainEvents

Utilizado para recuperar eventos en cadena (registro de ID, claves, alquiler de almacenamiento)

## API

| Nombre del Método                  | Tipo de Solicitud               | Tipo de Respuesta    | Descripción                                                                                                                    |
| ---------------------------------- | ------------------------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| GetOnChainSigner                   | SignerRequest                   | OnChainEvent         | Devuelve el evento en cadena para un firmante activo de un Fid                                                                 |
| GetOnChainSignersByFid             | FidRequest                      | OnChainEventResponse | Devuelve todos los eventos de adición de claves de cuenta (firmantes) activos para un Fid                                      |
| GetIdRegistryOnChainEvent          | FidRequest                      | OnChainEvent         | Devuelve el evento más reciente de registro/transferencia en cadena para un fid                                                |
| GetIdRegistryOnChainEventByAddress | IdRegistryEventByAddressRequest | OnChainEvent         | Devuelve el evento de registro/transferencia por dirección si existe (permite buscar fid por dirección)                        |
| GetOnChainEvents                   | OnChainEventRequest             | OnChainEventResponse | Devuelve todos los eventos en cadena filtrados por tipo para un Fid (incluye claves inactivas y eventos de alquiler expirados) |

## Solicitud de Firmante

| Campo  | Tipo        | Etiqueta | Descripción                                        |
| ------ | ----------- | -------- | -------------------------------------------------- |
| fid    | [uint64](#) |          | ID de Farcaster del usuario que generó el Firmante |
| signer | [bytes](#)  |          | Clave Pública del Firmante                         |

## Solicitud de Fid

| Campo      | Tipo        | Etiqueta | Descripción                          |
| ---------- | ----------- | -------- | ------------------------------------ |
| fid        | [uint64](#) |          | ID de Farcaster del usuario          |
| page_size  | uint32      |          | (opcional) Tipo de Enlace solicitado |
| page_token | bytes       |          | (opcional) Tipo de Enlace solicitado |
| reverse    | boolean     |          | (opcional) Orden de la respuesta     |

#### IdRegistryEventByAddressRequest

| Campo   | Tipo            | Etiqueta | Descripción |
| ------- | --------------- | -------- | ----------- |
| address | [bytes](#bytes) |          |             |

#### OnChainEventResponse

| Campo           | Tipo                          | Etiqueta | Descripción |
| --------------- | ----------------------------- | -------- | ----------- |
| events          | [OnChainEvent](#onchainevent) | repetido |             |
| next_page_token | [bytes](#bytes)               | opcional |             |
