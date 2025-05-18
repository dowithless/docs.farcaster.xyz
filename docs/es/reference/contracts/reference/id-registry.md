---
outline: [2, 3]
---

# Registro de IDs

El Registro de IDs registra qué fid está asociado con qué dirección de Ethereum, y gestiona las transferencias y recuperaciones de fids.

Si deseas leer información sobre un fid, transferirlo o recuperarlo, o gestionar su dirección de recuperación, utiliza el Registro de IDs.

Si deseas registrar un nuevo fid, utiliza en su lugar la [Puerta de Enlace de ID](/es/reference/contracts/reference/id-gateway.md).

## Lectura

### idOf

Devuelve el fid (`uint256`) propiedad de una dirección, o cero si la dirección no posee un fid.

| Parámetro | tipo      | Descripción                         |
| --------- | --------- | ----------------------------------- |
| owner     | `address` | La dirección a verificar por un fid |

### custodyOf

Devuelve la dirección de custodia (`address`) que posee un fid específico. Devuelve la dirección cero si el fid no existe.

| Parámetro | tipo      | Descripción                          |
| --------- | --------- | ------------------------------------ |
| fid       | `uint256` | El fid para encontrar su propietario |

### recoveryOf

Devuelve la dirección de recuperación (`address`) de un fid. Devuelve la dirección cero si el fid no existe.

| Parámetro | tipo      | Descripción                                        |
| --------- | --------- | -------------------------------------------------- |
| fid       | `uint256` | El fid para encontrar su dirección de recuperación |

### idCounter

Devuelve el fid más alto registrado hasta ahora (`uint256`).

### verifyFidSignature

Verifica que un mensaje fue firmado por la dirección de custodia actual de un fid. Devuelve un `bool`.

| Parámetro      | tipo      | Descripción                       |
| -------------- | --------- | --------------------------------- |
| custodyAddress | `address` | La dirección a verificar la firma |
| fid            | `uint256` | El fid asociado con la firma      |
| digest         | `bytes32` | Datos firmados hasheados          |
| sig            | `bytes`   | Firma a verificar                 |

### nonces

Devuelve el siguiente nonce (`uint256`) no utilizado para una dirección. Utilizado para generar firmas EIP-712.

| Parámetro | tipo      | Descripción                        |
| --------- | --------- | ---------------------------------- |
| owner     | `address` | La dirección para obtener el nonce |

## Escritura

### register

Revertirá si se llama directamente. Debe llamarse a través de la [Puerta de Enlace de ID](/es/reference/contracts/reference/id-gateway.md).

### changeRecoveryAddress

Cambia la dirección de recuperación para el fid del llamante.

| Parámetro | tipo      | Descripción                        |
| --------- | --------- | ---------------------------------- |
| recovery  | `address` | La nueva dirección de recuperación |

### transfer

Transfiere el fid del llamante a una nueva dirección. La dirección `to` debe firmar un mensaje EIP-712 [`Transfer`](#transfer-signature) aceptando la transferencia. La dirección `to` no debe poseer ya un fid.

| Parámetro | tipo      | Descripción                                                          |
| --------- | --------- | -------------------------------------------------------------------- |
| to        | `address` | Dirección a la que transferir el fid                                 |
| deadline  | `uint256` | Plazo de firma                                                       |
| sig       | `bytes`   | Firma EIP-712 [`Transfer`](#transfer-signature) de la dirección `to` |

::: warning
Transferir un fid no restablece su dirección de recuperación. Para transferir un fid y actualizar su dirección de recuperación, llama a [`transferAndChangeRecovery`](#transferandchangerecovery). Si estás recibiendo un fid de un remitente no confiable, asegúrate de que su dirección de recuperación se borre o cambie durante la transferencia.
:::

#### Firma de Transferencia

Para transferir un fid a otra cuenta, el llamante debe proporcionar una firma tipada EIP-712 de la dirección receptora en el siguiente formato:

`Transfer(uint256 fid,address to,uint256 nonce,uint256 deadline)`

| Parámetro | tipo      | Descripción                               |
| --------- | --------- | ----------------------------------------- |
| fid       | `uint256` | El fid siendo transferido                 |
| to        | `address` | La dirección que recibe el fid            |
| nonce     | `uint256` | Nonce actual de la dirección firmante     |
| deadline  | `uint256` | Marca de tiempo de expiración de la firma |

::: code-group

```ts [@farcaster/hub-web]
import { ViemWalletEip712Signer } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();

const eip712Signer = new ViemWalletEip712Signer(walletClient);
const signature = await eip712signer.signTransfer({
  fid: 1n,
  to: account,
  nonce,
  deadline,
});
```

```ts [Viem]
import { ID_REGISTRY_EIP_712_TYPES } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();

const signature = await walletClient.signTypedData({
  account,
  ...ID_REGISTRY_EIP_712_TYPES,
  primaryType: 'Transfer',
  message: {
    fid: 1n,
    to: account,
    nonce,
    deadline,
  },
});
```

```ts [helpers.ts]
import { ID_REGISTRY_ADDRESS, idRegistryABI } from '@farcaster/hub-web';
import { publicClient, account } from './clients.ts';

export const getDeadline = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHour = 60 * 60;
  return now + oneHour;
};

export const readNonce = async () => {
  return await publicClient.readContract({
    address: ID_REGISTRY_ADDRESS,
    abi: idRegistryABI,
    functionName: 'nonce',
    args: [account],
  });
};
```

<<< @/examples/contracts/clients.ts

:::

### transferAndChangeRecovery

Transfiere el fid del llamante a una nueva dirección _y_ cambia la dirección de recuperación del fid. Esto puede usarse para recibir de manera segura una transferencia de fid de una dirección no confiable.

La dirección receptora debe firmar un mensaje EIP-712 [`TransferAndChangeRecovery`](#transferandchangerecovery-signature) aceptando la transferencia. La dirección `to` no debe poseer ya un fid.

| Parámetro | tipo      | Descripción                                                                                  |
| --------- | --------- | -------------------------------------------------------------------------------------------- |
| to        | `address` | La dirección a la que transferir el fid                                                      |
| recovery  | `address` | La nueva dirección de recuperación                                                           |
| deadline  | `uint256` | Plazo de firma                                                                               |
| sig       | `bytes`   | Firma EIP-712 [`TransferAndChangeRecovery`](#transferandchangerecovery) de la dirección `to` |

#### Firma TransferAndChangeRecovery

Para transferir un fid a otra cuenta y cambiar la recuperación, debes proporcionar una firma tipada EIP-712 de la dirección `to` en el siguiente formato:

`TransferAndChangeRecovery(uint256 fid,address to,address recovery,uint256 nonce,uint256 deadline)`

| Parámetro | tipo      | Descripción                               |
| --------- | --------- | ----------------------------------------- |
| fid       | `uint256` | El fid siendo transferido                 |
| to        | `address` | La dirección que recibe el fid            |
| recovery  | `address` | La nueva dirección de recuperación        |
| nonce     | `uint256` | Nonce actual de la dirección firmante     |
| deadline  | `uint256` | Marca de tiempo de expiración de la firma |

::: code-group

```ts [@farcaster/hub-web]
import { ViemWalletEip712Signer } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();

const eip712Signer = new ViemWalletEip712Signer(walletClient);
const signature = await eip712signer.signTransferAndChangeRecovery({
  fid: 1n,
  to: account,
  recovery: '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7',
  nonce,
  deadline,
});
```

```ts [Viem]
import { ID_REGISTRY_EIP_712_TYPES } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();

const signature = await walletClient.signTypedData({
  account,
  ...ID_REGISTRY_EIP_712_TYPES,
  primaryType: 'TransferAndChangeRecovery',
  message: {
    fid: 1n,
    to: account,
    recovery: '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7',
    nonce,
    deadline,
  },
});
```

```ts [helpers.ts]
import { ID_REGISTRY_ADDRESS, idGatewayABI } from '@farcaster/hub-web';
import { publicClient, account } from './clients.ts';

export const getDeadline = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHour = 60 * 60;
  return now + oneHour;
};

export const readNonce = async () => {
  return await publicClient.readContract({
    address: ID_REGISTRY_ADDRESS,
    abi: idRegistryABI,
    functionName: 'nonce',
    args: [account],
  });
};
```

<<< @/examples/contracts/clients.ts

:::

### recover

Transfiere un fid a una nueva dirección si el llamante es la dirección de recuperación. La dirección `to` debe firmar un mensaje EIP-712 [`Transfer`](#transfer-signature) aceptando la transferencia.

La dirección `to` no debe poseer ya un fid.

| Parámetro | tipo      | Descripción                                                          |
| --------- | --------- | -------------------------------------------------------------------- |
| from      | `address` | La dirección desde la que transferir el fid                          |
| to        | `address` | La dirección a la que transferir el fid                              |
| deadline  | `uint256` | Plazo de firma                                                       |
| sig       | `bytes`   | Firma EIP-712 [`Transfer`](#transfer-signature) de la dirección `to` |

### changeRecoveryAddressFor

Cambia la dirección de recuperación de un fid en nombre del propietario proporcionando una firma. El propietario debe firmar una firma EIP-712 `ChangeRecoveryAddress` aprobando el cambio.

| Parámetro | tipo      | Descripción                                                                       |
| --------- | --------- | --------------------------------------------------------------------------------- |
| owner     | `address` | Dirección del propietario del fid                                                 |
| recovery  | `address` | La nueva dirección de recuperación                                                |
| deadline  | `uint256` | Plazo de firma                                                                    |
| sig       | `bytes`   | Firma EIP-712 [`ChangeRecoveryAddress`](#transfer-signature) de la dirección `to` |

### Firma ChangeRecoveryAddress

Para cambiar una dirección de recuperación en nombre del propietario de un fid, el llamante debe proporcionar una firma tipada EIP-712 de la dirección `owner` en el siguiente formato:

`ChangeRecoveryAddress(uint256 fid,address from,address to,uint256 nonce,uint256 deadline)`

| Parámetro | tipo      | Descripción                               |
| --------- | --------- | ----------------------------------------- |
| fid       | `uint256` | El fid del propietario                    |
| from      | `address` | La dirección de recuperación anterior     |
| to        | `address` | La nueva dirección de recuperación        |
| nonce     | `uint256` | Nonce actual de la dirección firmante     |
| deadline  | `uint256` | Marca de tiempo de expiración de la firma |

::: code-group

```ts [@farcaster/hub-web]
import { ViemWalletEip712Signer } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();

const eip712Signer = new ViemWalletEip712Signer(walletClient);
const signature = await eip712signer.signChangeRecoveryAddress({
  fid: 1n,
  from: '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7',
  to: '0xD7029BDEa1c17493893AAfE29AAD69EF892B8ff2',
  nonce,
  deadline,
});
```

```ts [Viem]
import { ID_REGISTRY_EIP_712_TYPES } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();

const signature = await walletClient.signTypedData({
  account,
  ...ID_REGISTRY_EIP_712_TYPES,
  primaryType: 'ChangeRecoveryAddress',
  message: {
    fid: 1n,
    from: '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7',
    to: '0xD7029BDEa1c17493893AAfE29AAD69EF892B8ff2',
    nonce,
    deadline,
  },
});
```

```ts [helpers.ts]
import { ID_REGISTRY_ADDRESS, idGatewayABI } from '@farcaster/hub-web';
import { publicClient, account } from './clients.ts';

export const getDeadline = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHour = 60 * 60;
  return now + oneHour;
};

export const readNonce = async () => {
  return await publicClient.readContract({
    address: ID_REGISTRY_ADDRESS,
    abi: idRegistryABI,
    functionName: 'nonces',
    args: [account],
  });
};
```

<<< @/examples/contracts/clients.ts

:::

### transferFor

Transfiere el fid propiedad de la dirección `from` a la dirección `to`. El llamante debe proporcionar dos firmas EIP-712 [`Transfer`](#transfer-signature): una de la dirección `from` autorizando la transferencia y otra de la dirección `to` aceptando la transferencia. Estos mensajes tienen el [mismo formato](#transfer-signature). La dirección `to` no debe poseer ya un fid.

| Parámetro    | tipo      | Descripción                                                            |
| ------------ | --------- | ---------------------------------------------------------------------- |
| from         | `address` | La dirección desde la que transferir el fid                            |
| to           | `address` | La dirección a la que transferir el fid                                |
| fromDeadline | `uint256` | Plazo de firma                                                         |
| fromSig      | `bytes`   | Firma EIP-712 [`Transfer`](#transfer-signature) de la dirección `from` |
| toDeadline   | `uint256` | Plazo de firma                                                         |
| toSig        | `bytes`   | Firma EIP-712 [`Transfer`](#transfer-signature) de la dirección `to`   |

### transferAndChangeRecoveryFor

Transfiere el fid propiedad de la dirección `from` a la dirección `to`, y cambia la dirección de recuperación del fid. Esto puede usarse para recibir de manera segura una transferencia de fid de una dirección no confiable.

El llamante debe proporcionar dos firmas EIP-712 [`TransferAndChangeRecovery`](#transferandchangerecovery-signature): una de la dirección `from` autorizando la transferencia y otra de la dirección `to` aceptando la transferencia. Estos mensajes tienen el [mismo formato](#transferandchangerecovery-signature). La dirección `to` no debe poseer ya un fid.

| Parámetro    | tipo      | Descripción                                                                                              |
| ------------ | --------- | -------------------------------------------------------------------------------------------------------- |
| from         | `address` | La dirección desde la que transferir el fid                                                              |
| to           | `address` | La dirección a la que transferir el fid                                                                  |
| recovery     | `address` | La nueva dirección de recuperación                                                                       |
| fromDeadline | `uint256` | Plazo de firma                                                                                           |
| fromSig      | `bytes`   | Firma EIP-712 [`TransferAndChangeRecovery`](#transferandchangerecovery-signature) de la dirección `from` |
| toDeadline   | `uint256` | Plazo de firma                                                                                           |
| toSig        | `bytes`   | Firma EIP-712 [`TransferAndChangeRecovery`](#transferandchangerecovery-signature) de la dirección `to`   |

### recoverFor

Transfiere un fid a una nueva dirección con una firma de la dirección de recuperación del fid. El llamante debe proporcionar dos firmas EIP-712 [`Transfer`](#transfer-signature): una de la dirección de recuperación autorizando la transferencia y otra de la dirección `to` aceptando la transferencia. Estos mensajes tienen el [mismo formato
