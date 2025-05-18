# API de Verificaciones

Se utiliza para obtener pruebas válidas o revocadas de propiedad de una dirección de Ethereum.

## API

| Nombre del Método               | Tipo de Solicitud   | Tipo de Respuesta | Descripción                                                |
| ------------------------------- | ------------------- | ----------------- | ---------------------------------------------------------- |
| GetVerification                 | VerificationRequest | Message           | Devuelve un VerificationAdd para una dirección de Ethereum |
| GetVerificationsByFid           | FidRequest          | MessagesResponse  | Devuelve todos los VerificationAdds realizados por un Fid  |
| GetAllVerificationMessagesByFid | FidRequest          | MessagesResponse  | Devuelve todas las Verificaciones realizadas por un Fid    |

## Solicitud de Verificación

| Campo   | Tipo        | Etiqueta | Descripción                                         |
| ------- | ----------- | -------- | --------------------------------------------------- |
| fid     | [uint64](#) |          | Farcaster ID del usuario que generó la Verificación |
| address | [bytes](#)  |          | Dirección de Ethereum que se está verificando       |
