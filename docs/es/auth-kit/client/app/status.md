# `status`

Obtiene el estado actual de una solicitud de autenticación de Farcaster.

Devuelve el estado actual de la solicitud, ya sea `'pending'` si la mini app de billetera de Farcaster del usuario aún no ha enviado una firma, o `'completed'` una vez que la mini app de billetera ha devuelto una respuesta.

En el estado `'completed'`, la respuesta incluye el mensaje generado de "Iniciar sesión con Farcaster", una firma de la dirección de custodia del usuario, el fid verificado del usuario e información de su perfil.

```ts
const status = await appClient.status({
  channelToken: '210f1718-427e-46a4-99e3-2207f21f83ec',
});
```

## Parámetros

| Parámetro      | Tipo     | Descripción                       | Requerido | Ejemplo                                |
| -------------- | -------- | --------------------------------- | --------- | -------------------------------------- |
| `channelToken` | `string` | Token de canal de Farcaster Auth. | Sí        | `8d0494d9-e0cf-402b-ab0a-394ac7fe07a0` |

## Retorna

```ts
{
    response: Response
    data: {
      state: "pending";
      nonce: string;
    } | {
      state: "completed";
      nonce: string;
      url: string;
      message: string;
      signature: `0x${string}`;
      fid: number;
      username?: string;
      bio?: string;
      displayName?: string;
      pfpUrl?: string;
      verifications?: Hex[];
      custody?: Hex;
    }
    isError: boolean
    error: Error
}
```

| Parámetro            | Descripción                                                                                                                            |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `response`           | Respuesta HTTP del servidor de retransmisión de Connect.                                                                               |
| `data.state`         | Estado de la solicitud de inicio de sesión, ya sea `"pending"` o `"complete"`.                                                         |
| `data.nonce`         | Nonce aleatorio utilizado en el mensaje SIWE. Si no proporcionas un nonce personalizado como argumento al hook, debes leer este valor. |
| `data.message`       | El mensaje SIWE generado.                                                                                                              |
| `data.signature`     | Firma hexadecimal producida por la billetera Warpcast del usuario.                                                                     |
| `data.fid`           | ID de Farcaster del usuario.                                                                                                           |
| `data.username`      | Nombre de usuario de Farcaster.                                                                                                        |
| `data.bio`           | Biografía de Farcaster del usuario.                                                                                                    |
| `data.displayName`   | Nombre para mostrar de Farcaster del usuario.                                                                                          |
| `data.pfpUrl`        | URL de la foto de perfil de Farcaster del usuario.                                                                                     |
| `data.custody`       | Dirección de custodia del FID del usuario.                                                                                             |
| `data.verifications` | Lista de direcciones verificadas del usuario.                                                                                          |
| `isError`            | Verdadero cuando ha ocurrido un error.                                                                                                 |
| `error`              | Instancia de `Error`.                                                                                                                  |
