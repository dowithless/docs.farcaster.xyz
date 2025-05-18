# `authenticate`

Envía un mensaje de Sign In With Farcaster, la firma del usuario y datos del perfil al servidor de retransmisión Connect.

```ts
const params = await walletClient.authenticate({
  message: 'example.com wants you to sign in with your Ethereum account…',
  signature: '0x9335c3055d47780411a3fdabad293c68c84ea350a11794cdc811fd5…',
  fid: 1,
  username: 'alice',
  bio: "I'm a little teapot who didn't fill out my bio",
  displayName: 'Alice Teapot',
  pfpUrl: 'https://images.example.com/profile.png',
});
```

## Parámetros

| Parámetro      | Tipo     | Descripción                                                                                         | Requerido |
| -------------- | -------- | --------------------------------------------------------------------------------------------------- | --------- |
| `authKey`      | `string` | Clave API de Farcaster Auth. Farcaster Auth v1 restringe las llamadas a `/authenticate` a Warpcast. | Sí        |
| `channelToken` | `string` | Token de canal de Farcaster Auth.                                                                   | Sí        |
| `message`      | `string` | El mensaje Sign in With Farcaster generado por tu aplicación de wallet y firmado por el usuario.    | Sí        |
| `message`      | `string` | El mensaje Sign in With Farcaster generado por tu aplicación de wallet y firmado por el usuario.    | Sí        |
| `signature`    | `Hex`    | Firma SIWE creada por la cuenta del usuario del wallet.                                             | Sí        |
| `fid`          | `number` | fid del usuario del wallet.                                                                         | Sí        |
| `username`     | `string` | Nombre de usuario Farcaster del usuario del wallet.                                                 | Sí        |
| `bio`          | `string` | Biografía del usuario del wallet.                                                                   | Sí        |
| `displayName`  | `string` | Nombre para mostrar del usuario del wallet.                                                         | Sí        |
| `pfpUrl`       | `string` | URL de la foto de perfil del usuario del wallet.                                                    | Sí        |

## Retorna

```ts
{
  response: Response
  data: {
      state: 'completed'
      nonce: string
      message?: string
      signature?: `Hex`
      fid?: number
      username?: string
      bio?: string
      displayName?: string
      pfpUrl?: string
  }
  isError: boolean
  error: Error
}
```

| Parámetro          | Descripción                                                                      |
| ------------------ | -------------------------------------------------------------------------------- |
| `response`         | Respuesta HTTP del servidor de retransmisión Connect.                            |
| `data.state`       | Estado de la solicitud de inicio de sesión, puede ser `"pending"` o `"complete"` |
| `data.nonce`       | Nonce aleatorio utilizado en el mensaje SIWE.                                    |
| `data.message`     | El mensaje SIWE generado.                                                        |
| `data.signature`   | Firma hexadecimal producida por el wallet Warpcast del usuario.                  |
| `data.fid`         | ID de Farcaster del usuario.                                                     |
| `data.username`    | Nombre de usuario Farcaster del usuario.                                         |
| `data.bio`         | Biografía Farcaster del usuario.                                                 |
| `data.displayName` | Nombre para mostrar Farcaster del usuario.                                       |
| `data.pfpUrl`      | URL de la foto de perfil Farcaster del usuario.                                  |
| `isError`          | Verdadero cuando ocurre un error.                                                |
| `error`            | Instancia de `Error`.                                                            |
