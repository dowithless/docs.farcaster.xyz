# API de Enlaces

Un Link (Enlace) representa una relación entre dos usuarios (por ejemplo, seguir)

## API

| Nombre del Método       | Tipo de Solicitud    | Tipo de Respuesta | Descripción                                                          |
| ----------------------- | -------------------- | ----------------- | -------------------------------------------------------------------- |
| GetLink                 | LinkRequest          | Message           | Devuelve un Link específico                                          |
| GetLinksByFid           | LinksByFidRequest    | MessagesResponse  | Devuelve Links creados por un fid en orden cronológico inverso       |
| GetLinksByTarget        | LinksByTargetRequest | MessagesResponse  | Devuelve LinkAdds para un objetivo dado en orden cronológico inverso |
| GetAllLinkMessagesByFid | FidRequest           | MessagesResponse  | Devuelve Links creados por un fid en orden cronológico inverso       |

## Solicitud de Link

| Campo      | Tipo        | Etiqueta | Descripción                                 |
| ---------- | ----------- | -------- | ------------------------------------------- |
| fid        | [uint64](#) |          | Farcaster ID del usuario que generó el Link |
| link_type  | [string](#) |          | Tipo del Link solicitado                    |
| target_fid | [uint64](#) |          | Fid del objetivo                            |

## Solicitud LinksByFid

| Campo      | Tipo        | Etiqueta | Descripción                                 |
| ---------- | ----------- | -------- | ------------------------------------------- |
| fid        | [uint64](#) |          | Farcaster ID del usuario que generó el Link |
| link_type  | string      |          | Tipo del Link solicitado                    |
| page_size  | uint32      |          | (opcional) Tipo del Link solicitado         |
| page_token | bytes       |          | (opcional) Tipo del Link solicitado         |
| reverse    | boolean     |          | (opcional) Orden de la respuesta            |

## Solicitud LinksByTarget

| Campo      | Tipo        | Etiqueta | Descripción                                 |
| ---------- | ----------- | -------- | ------------------------------------------- |
| target_fid | [uint64](#) |          | Farcaster ID del usuario que generó el Link |
| link_type  | string      |          | (opcional) Tipo del Link solicitado         |
| page_size  | uint32      |          | (opcional) Tipo del Link solicitado         |
| page_token | bytes       |          | (opcional) Tipo del Link solicitado         |
| reverse    | boolean     |          | (opcional) Orden de la respuesta            |
