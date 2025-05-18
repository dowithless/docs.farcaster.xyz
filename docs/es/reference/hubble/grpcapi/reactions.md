# API de Reacciones

## API

| Nombre del Método           | Tipo de Solicitud        | Tipo de Respuesta | Descripción                                                                |
| --------------------------- | ------------------------ | ----------------- | -------------------------------------------------------------------------- |
| GetReaction                 | ReactionRequest          | Message           | Devuelve una Reacción específica                                           |
| GetReactionsByFid           | ReactionsByFidRequest    | MessagesResponse  | Devuelve las Reacciones realizadas por un Fid en orden cronológico inverso |
| GetReactionsByCast          | ReactionsByCastRequest   | MessagesResponse  | Devuelve ReactionAdds para un Cast dado en orden cronológico inverso       |
| GetReactionsByTarget        | ReactionsByTargetRequest | MessagesResponse  | Devuelve ReactionAdds para un Cast dado en orden cronológico inverso       |
| GetAllReactionMessagesByFid | FidRequest               | MessagesResponse  | Devuelve las Reacciones realizadas por un Fid en orden cronológico inverso |

## Solicitud de Reacción

Utilizada para recuperar reacciones válidas o revocadas

| Campo          | Tipo              | Etiqueta | Descripción                                                      |
| -------------- | ----------------- | -------- | ---------------------------------------------------------------- |
| fid            | [uint64](#)       |          | Farcaster ID del usuario que generó la Reacción                  |
| reaction_type  | [ReactionType](#) |          | Tipo de Reacción solicitada                                      |
| target_cast_id | [CastId](#)       |          | (opcional) Identificador del Cast cuyas reacciones se solicitan  |
| target_url     | [string](#)       |          | (opcional) Identificador de la URL cuyas reacciones se solicitan |

## Solicitud ReactionsByFid

| Campo         | Tipo              | Etiqueta | Descripción                                     |
| ------------- | ----------------- | -------- | ----------------------------------------------- |
| fid           | [uint64](#)       |          | Farcaster ID del usuario que generó la Reacción |
| reaction_type | [ReactionType](#) |          | Tipo de Reacción solicitada                     |
| page_size     | uint32            |          | (opcional) Tipo de Enlace solicitado            |
| page_token    | bytes             |          | (opcional) Tipo de Enlace solicitado            |
| reverse       | boolean           |          | (opcional) Orden de la respuesta                |

## Solicitud ReactionsByCast

| Campo         | Tipo              | Etiqueta | Descripción                                          |
| ------------- | ----------------- | -------- | ---------------------------------------------------- |
| cast_id       | [CastId](#)       |          | Identificador del Cast cuyas reacciones se solicitan |
| reaction_type | [ReactionType](#) |          | Tipo de Reacción solicitada                          |
| page_size     | uint32            |          | (opcional) Tipo de Enlace solicitado                 |
| page_token    | bytes             |          | (opcional) Tipo de Enlace solicitado                 |
| reverse       | boolean           |          | (opcional) Orden de la respuesta                     |

## Solicitud ReactionsByTargetRequest

| Campo          | Tipo                          | Etiqueta | Descripción |
| -------------- | ----------------------------- | -------- | ----------- |
| target_cast_id | [CastId](#CastId)             |          |             |
| target_url     | [string](#string)             |          |             |
| reaction_type  | [ReactionType](#ReactionType) | opcional |             |
| page_size      | [uint32](#uint32)             | opcional |             |
| page_token     | [bytes](#bytes)               | opcional |             |
| reverse        | [bool](#bool)                 | opcional |             |
