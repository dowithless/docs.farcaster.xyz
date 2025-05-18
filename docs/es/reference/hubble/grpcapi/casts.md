# API de Casts

Se utiliza para recuperar casts válidos o tumbas (tombstones) para casts eliminados.

## API

| Nombre del Método       | Tipo de Solicitud | Tipo de Respuesta | Descripción                                                             |
| ----------------------- | ----------------- | ----------------- | ----------------------------------------------------------------------- |
| GetCast                 | CastId            | Message           | Devuelve un cast específico                                             |
| GetCastsByFid           | FidRequest        | MessagesResponse  | Devuelve CastAdds para un Fid en orden cronológico inverso              |
| GetCastsByParent        | CastId            | MessagesResponse  | Devuelve respuestas CastAdd a un cast dado en orden cronológico inverso |
| GetCastsByMention       | FidRequest        | MessagesResponse  | Devuelve CastAdds que mencionan un Fid en orden cronológico inverso     |
| GetAllCastMessagesByFid | FidRequest        | MessagesResponse  | Devuelve casts para un Fid en orden cronológico inverso                 |

## CastsByParentRequest

| Campo          | Tipo              | Etiqueta | Descripción |
| -------------- | ----------------- | -------- | ----------- |
| parent_cast_id | [CastId](#CastId) |          |             |
| parent_url     | [string](#string) |          |             |
| page_size      | [uint32](#uint32) | opcional |             |
| page_token     | [bytes](#bytes)   | opcional |             |
| reverse        | [bool](#bool)     | opcional |             |
