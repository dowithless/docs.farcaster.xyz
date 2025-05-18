# `buildSignInMessage`

Construye un mensaje de "Sign In With Farcaster" para presentar al usuario final y que lo firme.

Añade los atributos requeridos del mensaje "Sign In With Farcaster" a cualquier parámetro proporcionado. Debes analizar la mayoría de estos parámetros a partir de un URI de protocolo proporcionado. Tu aplicación de billetera debe proporcionar la dirección de custodia y el fid del usuario.

Retorna un objeto `SiweMessage` y el mensaje como una cadena de texto.

```ts
const { siweMessage, message } = walletClient.buildSignInMessage({
  address: '0x63C378DDC446DFf1d831B9B96F7d338FE6bd4231',
  fid: 1,
  uri: 'https://example.com/login',
  domain: 'example.com',
  nonce: 'ESsxs6MaFio7OvqWb',
});
```

## Parámetros

| Parámetro        | Tipo     | Descripción                                                                                                                                                                                                                                          | Requerido |
| ---------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| `address`        | `Hex`    | Dirección de custodia del usuario de la billetera. Esta dirección debe ser la misma cuenta que firma el mensaje generado de "Sign In With Farcaster". Tu aplicación de billetera debe proporcionar la dirección de custodia del usuario autenticado. | Sí        |
| `fid`            | `string` | Fid del usuario de la billetera. Tu aplicación de billetera debe proporcionar el fid del usuario autenticado.                                                                                                                                        | Sí        |
| `uri`            | `string` | URL de inicio de sesión de la aplicación conectada que depende. Analiza esto a partir del URI proporcionado de "Sign In With Farcaster".                                                                                                             | Sí        |
| `domain`         | `string` | Dominio de la aplicación conectada que depende. Analiza esto a partir del URI proporcionado de "Sign In With Farcaster".                                                                                                                             | Sí        |
| `nonce`          | `string` | Nonce aleatorio para incluir en el mensaje de "Sign In With Farcaster". Debe tener al menos 8 caracteres alfanuméricos. Analiza esto a partir del URI proporcionado de "Sign In With Farcaster".                                                     | Sí        |
| `notBefore`      | `string` | Hora de inicio en la que la firma SIWE se vuelve válida. Fecha y hora en formato ISO 8601. Analiza esto a partir del URI proporcionado de "Sign In With Farcaster".                                                                                  | No        |
| `expirationTime` | `string` | Hora de expiración después de la cual la firma SIWE deja de ser válida. Fecha y hora en formato ISO 8601. Analiza esto a partir del URI proporcionado de "Sign In With Farcaster".                                                                   | No        |
| `requestId`      | `string` | Un ID específico del sistema proporcionado por la aplicación que depende. Analiza esto a partir del URI proporcionado de "Sign In With Farcaster".                                                                                                   | No        |

## Retorna

```ts
{
  siweMessage: SiweMessage;
  message: string;
  isError: boolean;
  error: Error;
}
```

| Parámetro     | Descripción                                           |
| ------------- | ----------------------------------------------------- |
| `siweMessage` | Objeto de mensaje "Sign In With Ethereum" construido. |
| `message`     | Mensaje SIWE serializado como una cadena de texto.    |
| `isError`     | Verdadero cuando ha ocurrido un error.                |
| `error`       | Instancia de `Error`.                                 |
