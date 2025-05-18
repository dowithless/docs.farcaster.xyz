# API de Mensajes

Se utiliza para validar y enviar un mensaje al Hub de Farcaster. Los mensajes válidos son aceptados y difundidos a otros hubs en la red.

## API

| Nombre del Método | Tipo de Solicitud | Tipo de Respuesta  | Descripción                                              |
| ----------------- | ----------------- | ------------------ | -------------------------------------------------------- |
| SubmitMessage     | Message           | Message            | Envía un Mensaje al Hub                                  |
| ValidateMessage   | Message           | ValidationResponse | Valida un Mensaje en el Hub sin fusionarlo ni difundirlo |

## ValidationResponse

| Campo   | Tipo    | Etiqueta | Descripción                                               |
| ------- | ------- | -------- | --------------------------------------------------------- |
| valid   | boolean |          | Indica si el mensaje es válido o no                       |
| message | Message |          | El mensaje que se está validando (igual que la solicitud) |
