# `parseSignInURI`

Analiza el URI de "Sign In With Farcaster" proporcionado por un usuario de la aplicación conectada.

Devuelve los parámetros analizados. Tu aplicación debe usarlos para construir un mensaje de "Sign In With Farcaster".

Devuelve un error si el URI no es válido.

```ts
const params = walletClient.parseSignInURI({
  uri: 'farcaster://connect?channelToken=76be6229-bdf7-4ad2-930a-540fb2de1e08&nonce=ESsxs6MaFio7OvqWb&siweUri=https%3A%2F%2Fexample.com%2Flogin&domain=example.com',
});
```

## Parámetros

| Parámetro | Tipo     | Descripción                      | Requerido |
| --------- | -------- | -------------------------------- | --------- |
| `uri`     | `string` | URI de "Sign In With Farcaster". | Sí        |

## Devuelve

```ts
{
  channelToken: string
  params: {
    domain: string
    uri: string
    nonce: string
    notBefore?: string
    expirationTime?: string
    requestId?: string
  }
  isError: boolean
  error: Error
}
```

| Parámetro               | Descripción                                                                       |
| ----------------------- | --------------------------------------------------------------------------------- |
| `channelToken`          | Token UUID del canal de retransmisión de conexión.                                |
| `params.uri`            | URI de inicio de sesión de la aplicación conectada dependiente.                   |
| `params.domain`         | Dominio de la aplicación dependiente.                                             |
| `params.nonce`          | Nonce aleatorio proporcionado por la aplicación dependiente.                      |
| `params.notBefore`      | Momento en que este mensaje se vuelve válido.                                     |
| `params.expirationTime` | Momento en que este mensaje expira.                                               |
| `params.requestId`      | Identificador específico del sistema proporcionado por la aplicación dependiente. |
| `isError`               | Verdadero cuando ha ocurrido un error.                                            |
| `error`                 | Instancia de `Error`.                                                             |
