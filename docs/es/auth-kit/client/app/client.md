# Cliente de Aplicación

Si estás construyendo una [aplicación conectada](https://docs.farcaster.xyz/learn/what-is-farcaster/apps#connected-apps) y deseas que los usuarios inicien sesión con Farcaster, utiliza un `AppClient`.

Puedes usar un `AppClient` para crear un canal de retransmisión de autenticación de Farcaster, generar un enlace profundo para solicitar una firma desde la aplicación de billetera Farcaster del usuario y verificar la firma devuelta.

```ts
import { createAppClient, viemConnector } from '@farcaster/auth-client';

const appClient = createAppClient({
  relay: 'https://relay.farcaster.xyz',
  ethereum: viemConnector(),
});
```

## Parámetros

| Parámetro  | Tipo                | Descripción                                                                                                                                                                                                                                                                                                    | Requerido |
| ---------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| `ethereum` | `EthereumConnector` | <p>Un conector de Ethereum, utilizado para consultar los contratos de Farcaster y verificar firmas de billeteras de contratos inteligentes. `@farcaster/auth-client` actualmente solo proporciona el tipo de conector `viem`.</p> <p>Para usar un RPC personalizado, pasa una URL de RPC al conector viem.</p> | Sí        |
| `relay`    | `string`            | URL del servidor de retransmisión. Por defecto es el relay público en `https://relay.farcaster.xyz`                                                                                                                                                                                                            | No        |
| `version`  | `string`            | Versión de autenticación de Farcaster. Por defecto es `"v1"`                                                                                                                                                                                                                                                   | No        |
