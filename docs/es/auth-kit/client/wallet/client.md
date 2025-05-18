# Cliente de Wallet

Si estás desarrollando una [wallet app](https://docs.farcaster.xyz/learn/what-is-farcaster/apps#wallet-apps) y recibes solicitudes de firma, utiliza un `WalletClient`.

Puedes usar un `WalletClient` para analizar una URL de solicitud entrante de Sign In With Farcaster, construir un mensaje de Sign In With Farcaster para presentar al usuario y enviar el mensaje firmado a un canal de retransmisión de Farcaster Auth.

```ts
import { createWalletClient, viemConnector } from '@farcaster/auth-client';

const walletClient = createWalletClient({
  relay: 'https://relay.farcaster.xyz',
  ethereum: viemConnector(),
});
```

## Parámetros

| Parámetro  | Tipo                | Descripción                                                                                                                                                                                                                                                                                                 | Requerido |
| ---------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- |
| `ethereum` | `EthereumConnector` | <p>Un conector de Ethereum, utilizado para consultar los contratos de Farcaster y verificar firmas de wallets de contratos inteligentes. `@farcaster/auth-client` actualmente solo proporciona el tipo de conector `viem`.</p> <p>Para usar un RPC personalizado, pasa una URL de RPC al conector viem.</p> | Sí        |
| `relay`    | `string`            | URL del servidor de retransmisión. Por defecto es el relay público en `https://relay.farcaster.xyz`                                                                                                                                                                                                         | No        |
| `version`  | `string`            | Versión de Farcaster Auth. Por defecto es `"v1"`                                                                                                                                                                                                                                                            | No        |
