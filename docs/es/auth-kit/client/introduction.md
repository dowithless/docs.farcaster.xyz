# Cliente de Autenticación

[![Versión NPM](https://img.shields.io/npm/v/@farcaster/auth-client)](https://www.npmjs.com/package/@farcaster/auth-client)

La biblioteca `@farcaster/auth-client` proporciona un cliente independiente del framework para Farcaster Auth. Si no estás usando React, deseas mayor personalización o quieres interactuar directamente con el relay de Farcaster Auth, puedes usar esta biblioteca para construir tu propio flujo de "Iniciar sesión con Farcaster".

## Comenzando

### Instalación

Instala el cliente de autenticación y su dependencia [viem](https://viem.sh/).

```sh
npm install @farcaster/auth-client viem
```

**Nota:** Esta es una biblioteca cliente de bajo nivel. Si estás usando React, revisa [auth-kit](../) en su lugar.

### Crear un cliente

Configura un cliente con una URL del servidor relay y un conector Ethereum.

```tsx
import { createAppClient, viemConnector } from '@farcaster/auth-client';

const appClient = createAppClient({
  relay: 'https://relay.farcaster.xyz',
  ethereum: viemConnector(),
});
```

Dependiendo del tipo de aplicación que estés construyendo, puedes usar un `AppClient` o un `WalletClient`. Si estás construyendo una aplicación conectada y autenticando usuarios, usa un _app client_. Si estás construyendo una aplicación de billetera Farcaster, usa un _wallet client_.

### Consumir acciones

Ahora que tu cliente está configurado, puedes usarlo para interactuar con las acciones de Farcaster Auth.

```tsx
const { data: { channelToken } } = await appClient.createChannel({
    siweUri: "https://example.com/login",
    domain: "example.com";
});

const status = await appClient.watchStatus({
    channelToken,
});
```
