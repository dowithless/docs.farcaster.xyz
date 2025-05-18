---
outline: [2, 3]
---

# ID Gateway

El ID Gateway registra nuevos Farcaster IDs y los añade al [Id Registry](/es/reference/contracts/reference/id-registry.md).

Si deseas crear un nuevo Farcaster ID, utiliza el ID Gateway.

## Lectura

### price

Obtiene el precio en wei para registrar un fid. Esto incluye el precio de 1 unidad de almacenamiento. Usa el parámetro `extraStorage` para incluir unidades adicionales de almacenamiento en el precio total.

| Parámetro    | tipo                 | Descripción                                      |
| ------------ | -------------------- | ------------------------------------------------ |
| extraStorage | `uint256` (opcional) | Número de unidades de almacenamiento adicionales |

### nonces

Obtiene el siguiente nonce sin usar para una dirección. Se utiliza para generar una firma EIP-712 [`Register`](#register-signature) para [registerFor](#registerfor).

| Parámetro | tipo      | Descripción                     |
| --------- | --------- | ------------------------------- |
| owner     | `address` | Dirección para obtener el nonce |

## Escritura

### register

Registra un nuevo fid para el llamador y paga por el almacenamiento. El llamador no debe poseer ya un fid.

| Parámetro    | tipo                 | Descripción                                                 |
| ------------ | -------------------- | ----------------------------------------------------------- |
| `msg.value`  | `wei`                | Cantidad a pagar por el registro                            |
| recovery     | `address`            | Dirección de recuperación para el nuevo fid                 |
| extraStorage | `uint256` (opcional) | Número de unidades de almacenamiento adicionales a alquilar |

### registerFor

Registra un nuevo fid para una dirección específica y paga por el almacenamiento. La dirección receptora debe firmar un mensaje EIP-712 [`Register`](#register-signature) aprobando el registro. El receptor no debe poseer ya un fid.

| Parámetro    | tipo                 | Descripción                                   |
| ------------ | -------------------- | --------------------------------------------- |
| `msg.value`  | `wei`                | Cantidad a pagar por el registro              |
| to           | `address`            | Dirección a la que se registrará el fid       |
| recovery     | `address`            | Dirección de recuperación para el nuevo fid   |
| deadline     | `uint256`            | Marca de tiempo de expiración de la firma     |
| sig          | `bytes`              | Firma EIP-712 `Register` de la dirección `to` |
| extraStorage | `uint256` (opcional) | Unidades de almacenamiento adicionales        |

#### Firma Register

Para registrar un fid en nombre de otra cuenta, debes proporcionar una firma tipada EIP-712 de la dirección receptora en el siguiente formato:

`Register(address to,address recovery,uint256 nonce,uint256 deadline)`

| Parámetro | tipo      | Descripción                                                                                     |
| --------- | --------- | ----------------------------------------------------------------------------------------------- |
| to        | `address` | Dirección a la que se registrará el fid. El mensaje tipado debe ser firmado por esta dirección. |
| recovery  | `address` | Dirección de recuperación para el nuevo fid                                                     |
| nonce     | `uint256` | Nonce actual de la dirección `to`                                                               |
| deadline  | `uint256` | Marca de tiempo de expiración de la firma                                                       |

::: code-group

```ts [@farcaster/hub-web]
import { ViemWalletEip712Signer } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();

const eip712Signer = new ViemWalletEip712Signer(walletClient);
const signature = await eip712signer.signRegister({
  to: account,
  recovery: '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7',
  nonce,
  deadline,
});
```

```ts [Viem]
import { ID_GATEWAY_EIP_712_TYPES } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();

const signature = await walletClient.signTypedData({
  account,
  ...ID_GATEWAY_EIP_712_TYPES,
  primaryType: 'Register',
  message: {
    to: account,
    recovery: '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7',
    nonce,
    deadline,
  },
});
```

```ts [helpers.ts]
import { ID_GATEWAY_ADDRESS, idGatewayABI } from '@farcaster/hub-web';
import { publicClient, account } from './clients.ts';

export const getDeadline = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHour = 60 * 60;
  return now + oneHour;
};

export const readNonce = async () => {
  return await publicClient.readContract({
    address: ID_GATEWAY_ADDRESS,
    abi: idGatewayABI,
    functionName: 'nonces',
    args: [account],
  });
};
```

<<< @/examples/contracts/clients.ts

:::

## Errores

| Error            | Selector   | Descripción                                                                                                           |
| ---------------- | ---------- | --------------------------------------------------------------------------------------------------------------------- |
| InvalidSignature | `8baa579f` | La firma proporcionada es inválida. Puede estar mal formateada o firmada por una dirección incorrecta.                |
| SignatureExpired | `0819bdcd` | La firma proporcionada ha expirado. Obtén una nueva firma del firmante con una marca de tiempo de deadline posterior. |

## Fuente

[`IdGateway.sol`](https://github.com/farcasterxyz/contracts/blob/1aceebe916de446f69b98ba1745a42f071785730/src/IdGateway.sol)
