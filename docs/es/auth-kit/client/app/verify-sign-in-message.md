# `verifySignInMessage`

Verifica un mensaje de "Sign In With Farcaster". Tu aplicación debe llamar a esta función y comprobar que tiene éxito después de leer el mensaje y la firma proporcionados por la wallet de Farcaster del usuario a través del canal Connect.

Devuelve el mensaje de "Sign in With Farcaster" analizado, el fid del usuario y si la verificación tuvo éxito.

```ts
const { data, success, fid } = await appClient.verifySignInMessage({
  nonce: 'abcd1234',
  domain: 'example.com',
  message: 'example.com wants you to sign in with your Ethereum account…',
  signature: '0x9335c3055d47780411a3fdabad293c68c84ea350a11794cd11fd51b…',
});
```

## Parámetros

| Parámetro   | Tipo                     | Descripción                                                                                                                                                                                        | Requerido |
| ----------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| `domain`    | `string`                 | Dominio de tu aplicación. Debe coincidir con el dominio en el mensaje SIWF proporcionado.                                                                                                          | Sí        |
| `nonce`     | `string`                 | Un nonce personalizado. Debe coincidir con el nonce en el mensaje SIWF proporcionado.                                                                                                              | Sí        |
| `message`   | `string` o `SiweMessage` | El mensaje "Sign in With Farcaster" a verificar. Puede ser una cadena de texto o un `SiweMessage` analizado. Tu aplicación debe leer este valor del canal Connect una vez completada la solicitud. | Sí        |
| `signature` | `Hex`                    | Firma proporcionada por la wallet de Farcaster del usuario. Tu aplicación debe leer esto del canal Connect una vez completada la solicitud.                                                        | Sí        |

## Retorna

```ts
{
    data: SiweMessage,
    success: boolean,
    fid: number
    isError: boolean
    error: Error
}
```

| Parámetro | Descripción                                        |
| --------- | -------------------------------------------------- |
| `data`    | Mensaje SIWF analizado, como objeto `SiweMessage`. |
| `success` | True si la firma proporcionada es válida.          |
| `fid`     | FID del usuario.                                   |
| `isError` | True cuando ocurre un error.                       |
| `error`   | Instancia de `Error`.                              |
