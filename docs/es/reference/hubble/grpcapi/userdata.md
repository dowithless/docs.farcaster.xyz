# API de UserData

Se utiliza para recuperar los metadatos actuales asociados a un usuario.

## API

| Nombre del Método           | Tipo de Solicitud | Tipo de Respuesta | Descripción                                 |
| --------------------------- | ----------------- | ----------------- | ------------------------------------------- |
| GetUserData                 | UserDataRequest   | Message           | Devuelve un UserData específico para un Fid |
| GetUserDataByFid            | FidRequest        | MessagesResponse  | Devuelve todos los UserData para un Fid     |
| GetAllUserDataMessagesByFid | FidRequest        | MessagesResponse  | Devuelve todos los UserData para un Fid     |

## Solicitud UserData

| Campo          | Tipo              | Etiqueta | Descripción                                     |
| -------------- | ----------------- | -------- | ----------------------------------------------- |
| fid            | [uint64](#)       |          | Farcaster ID del usuario que generó el UserData |
| user_data_type | [UserDataType](#) |          | Tipo de UserData solicitado                     |

## Respuesta Messages

| Campo           | Tipo            | Etiqueta | Descripción       |
| --------------- | --------------- | -------- | ----------------- |
| messages        | [Message](#)    | repeated | Mensaje Farcaster |
| next_page_token | [bytes](#bytes) | optional |                   |
