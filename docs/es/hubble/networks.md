# Redes

Una cuenta de Farcaster debe elegir a qué red publica sus mensajes. Cualquier cuenta registrada en los [contratos](../learn/architecture/contracts.md) puede publicar en cualquier red. Sin embargo, los mensajes publicados en una red no son visibles en ninguna otra.

Existen dos redes principales:

- **Testnet** - la versión beta más reciente destinada a desarrolladores
- **Mainnet** - la versión estable que todos utilizan

Al [instalar tu hub](./install.md), deberás elegir una red a la cual conectarte.

## Testnet

Testnet es un entorno de pruebas para que los desarrolladores prueben nuevas funciones. Se transmiten mensajes ficticios cada 10 segundos para simular actividad.

Configura las siguientes variables en tu archivo .env en `apps/hubble`:

```sh
FC_NETWORK_ID=2
BOOTSTRAP_NODE=/dns/testnet1.farcaster.xyz/tcp/2282
```

Si ejecutas desde el código fuente, añade estos argumentos al comando `yarn start`:

```sh
yarn start ... \
    -n 2 \
    -b /dns/testnet1.farcaster.xyz/tcp/2282
```

## Mainnet

Mainnet es el entorno de producción que todos utilizan.

Configura las siguientes variables en tu archivo .env en `apps/hubble`:

```sh
FC_NETWORK_ID=1
BOOTSTRAP_NODE=/dns/hoyt.farcaster.xyz/tcp/2282
```

Si ejecutas desde el código fuente, añade estos argumentos al comando `yarn start`:

```sh
yarn start ... \
    -n 1 \
    -b /dns/hoyt.farcaster.xyz/tcp/2282
```
