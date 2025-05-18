# `createChannel`

Crea un canal de retransmisión de autenticación Farcaster.

Devuelve un token secreto que identifica el canal y una URI para mostrar al usuario final como enlace o código QR.

```ts
const channel = await appClient.createChannel({
  siweUri: 'https://example.com/login',
  domain: 'example.com',
});
```

## Parámetros

| Parámetro        | Tipo     | Descripción                                                                                           | Requerido | Ejemplo                                |
| ---------------- | -------- | ----------------------------------------------------------------------------------------------------- | --------- | -------------------------------------- |
| `siweUri`        | `string` | URL de inicio de sesión para tu aplicación.                                                           | Sí        | `https://example.com/login`            |
| `domain`         | `string` | Dominio de tu aplicación.                                                                             | Sí        | `example.com`                          |
| `nonce`          | `string` | Un nonce personalizado. Debe tener al menos 8 caracteres alfanuméricos.                               | No        | `ESsxs6MaFio7OvqWb`                    |
| `notBefore`      | `string` | Hora de inicio en la que la firma se vuelve válida. Fecha y hora ISO 8601.                            | No        | `2023-12-20T23:21:24.917Z`             |
| `expirationTime` | `string` | Hora de expiración en la que la firma deja de ser válida. Fecha y hora ISO 8601.                      | No        | `2023-12-20T23:21:24.917Z`             |
| `requestId`      | `string` | Un ID específico del sistema que tu app puede usar para referirse a la solicitud de inicio de sesión. | No        | `8d0494d9-e0cf-402b-ab0a-394ac7fe07a0` |

## Retorna

```ts
{
  response: Response;
  data: {
    channelToken: string;
    url: string;
    nonce: string;
  }
  isError: boolean;
  error: Error;
}
```

| Parámetro           | Descripción                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------ |
| `response`          | Respuesta HTTP del servidor de retransmisión Connect.                                      |
| `data.channelToken` | Token UUID del canal de retransmisión Connect.                                             |
| `data.url`          | URL de Sign in With Farcaster para presentar al usuario. Enlaza al cliente Warpcast en v1. |
| `data.nonce`        | Nonce aleatorio incluido en el mensaje de Sign in With Farcaster.                          |
| `isError`           | Verdadero cuando ocurre un error.                                                          |
| `error`             | Instancia de `Error`.                                                                      |
