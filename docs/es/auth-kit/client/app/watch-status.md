# `watchStatus`

Sondea el estado actual de una solicitud de autenticación de Farcaster.

Cuando el estado cambia a `'complete'`, esta acción se resuelve con el valor final del canal, incluyendo el mensaje de Sign In With Farcaster, la firma y la información del perfil del usuario.

```ts
const status = await appClient.watchStatus({
  channelToken: '210f1718-427e-46a4-99e3-2207f21f83ec',
  timeout: 60_000,
  interval: 1_000,
  onResponse: ({ response, data }) => {
    console.log('Response code:', response.status);
    console.log('Status data:', data);
  },
});
```

## Parámetros

| Parámetro      | Tipo       | Descripción                                                                                                                                                                                             | Requerido | Ejemplo                                |
| -------------- | ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- | -------------------------------------- |
| `channelToken` | `string`   | Token del canal de autenticación de Farcaster.                                                                                                                                                          | Sí        | `8d0494d9-e0cf-402b-ab0a-394ac7fe07a0` |
| `timeout`      | `number`   | Tiempo de espera para el sondeo, en milisegundos. Si la solicitud de conexión no se completa antes del tiempo de espera, `watchStatus` devuelve un error.                                               | No        | `300_000`                              |
| `interval`     | `number`   | Intervalo de sondeo, en milisegundos. El cliente verificará las actualizaciones con esta frecuencia.                                                                                                    | No        | `1_000`                                |
| `onResponse`   | `function` | Función de callback que se invoca cada vez que el cliente sondea una actualización y recibe una respuesta del servidor de retransmisión. Recibe el valor de retorno de la última solicitud de `status`. | No        | `({ data }) => console.log(data.fid)`  |

## Retorna

```ts
{
    response: Response
    data: {
        state: 'pending' | 'completed'
        nonce: string
        message?: string
        signature?: Hex
        fid?: number
        username?: string
        bio?: string
        displayName?: string
        pfpUrl?: string
        custody?: Hex;
        verifications?: Hex[];
    }
    isError: boolean
    error: Error
}
```

| Parámetro            | Descripción                                                                                                                               |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| `response`           | Respuesta HTTP del servidor de retransmisión de Connect.                                                                                  |
| `data.state`         | Estado de la solicitud de inicio de sesión, ya sea `"pending"` o `"complete"`.                                                            |
| `data.nonce`         | Nonce aleatorio utilizado en el mensaje SIWE. Si no proporcionas un nonce personalizado como argumento al hook, deberías leer este valor. |
| `data.message`       | El mensaje SIWE generado.                                                                                                                 |
| `data.signature`     | Firma hexadecimal producida por la billetera Warpcast del usuario.                                                                        |
| `data.fid`           | ID de Farcaster del usuario.                                                                                                              |
| `data.username`      | Nombre de usuario de Farcaster.                                                                                                           |
| `data.bio`           | Biografía de Farcaster del usuario.                                                                                                       |
| `data.displayName`   | Nombre para mostrar de Farcaster del usuario.                                                                                             |
| `data.pfpUrl`        | URL de la foto de perfil de Farcaster del usuario.                                                                                        |
| `data.custody`       | Dirección de custodia del FID del usuario.                                                                                                |
| `data.verifications` | Lista de direcciones verificadas del usuario.                                                                                             |
| `isError`            | Verdadero cuando ocurre un error.                                                                                                         |
| `error`              | Instancia de `Error`.                                                                                                                     |
