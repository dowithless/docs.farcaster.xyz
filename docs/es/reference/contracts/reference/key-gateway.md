---
outline: [2, 3]
---

# Key Gateway

El Key Gateway añade nuevas claves al [Registro de Claves](/es/reference/contracts/reference/key-registry.md).

Si deseas agregar una nueva clave pública a una cuenta de Farcaster, utiliza el Key Gateway.

## Lectura

### nonces

Devuelve el próximo nonce disponible para una dirección. Se utiliza para generar firmas EIP-712 en [addFor](#addFor).

| Parámetro | tipo      | Descripción                          |
| --------- | --------- | ------------------------------------ |
| owner     | `address` | La dirección para consultar el nonce |

## Escritura

### add

Añade una nueva clave para el fid del llamante y establece su estado como `Added`. Revierte si la clave ya está registrada.

| Parámetro    | tipo     | Descripción                                                                                                                                  |
| ------------ | -------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| keyType      | `uint32` | Debe ser `1`. Actualmente es el único `keyType` soportado.                                                                                   |
| key          | `bytes`  | La clave pública a añadir                                                                                                                    |
| metadataType | `uint8`  | Debe ser `1`. Actualmente es el único `metadataType` soportado.                                                                              |
| metadata     | `bytes`  | Codificado como [`SignedKeyRequestMetadata`](/es/reference/contracts/reference/signed-key-request-validator#signedkeyrequestmetadata-struct) |

### addFor

Añade una clave en nombre de otro fid proporcionando una firma. El propietario debe firmar un mensaje EIP-712 `Add` aprobando la clave. Revierte si la clave ya está registrada.

| Parámetro    | tipo      | Descripción                                                                                                                                  |
| ------------ | --------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| fidOwner     | `address` | Dirección del propietario del fid                                                                                                            |
| keyType      | `uint32`  | Debe ser `1`. Actualmente es el único `keyType` soportado.                                                                                   |
| key          | `bytes`   | La clave pública a añadir                                                                                                                    |
| metadataType | `uint8`   | Debe ser `1`. Actualmente es el único `metadataType` soportado.                                                                              |
| metadata     | `bytes`   | Codificado como [`SignedKeyRequestMetadata`](/es/reference/contracts/reference/signed-key-request-validator#signedkeyrequestmetadata-struct) |
| deadline     | `uint256` | Marca de tiempo de expiración de la firma                                                                                                    |
| sig          | `bytes`   | Firma EIP-712 [`Add`](/es/reference/contracts/reference/key-gateway#add-signature) del `fidOwner`                                            |

#### Firma Add

Para añadir una clave en nombre de otra cuenta, debes proporcionar una firma tipada EIP-712 de la cuenta con el siguiente formato:

`Add(address owner,uint32 keyType,bytes key,uint8 metadataType,bytes metadata,uint256 nonce,uint256 deadline)`

| Parámetro    | tipo      | Descripción                                                                                                                                  |
| ------------ | --------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| owner        | `address` | Dirección propietaria del fid. El mensaje tipado debe estar firmado por esta dirección.                                                      |
| keyType      | `uint32`  | Debe ser `1`. Actualmente es el único `keyType` soportado.                                                                                   |
| key          | `bytes`   | La clave pública a añadir                                                                                                                    |
| metadataType | `uint8`   | Debe ser `1`. Actualmente es el único `metadataType` soportado.                                                                              |
| metadata     | `bytes`   | Codificado como [`SignedKeyRequestMetadata`](/es/reference/contracts/reference/signed-key-request-validator#signedkeyrequestmetadata-struct) |
| nonce        | `uint256` | Nonce actual de la dirección `owner`                                                                                                         |
| deadline     | `uint256` | Marca de tiempo de expiración de la firma                                                                                                    |

::: code-group

```ts [@farcaster/hub-web]
import { ViemWalletEip712Signer } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { getPublicKey } from './signer.ts';
import { readNonce, getDeadline } from './helpers.ts';

const publicKey = await getPublicKey();
const metadata = await getMetadata();
const nonce = await readNonce();
const deadline = getDeadline();

const eip712Signer = new ViemWalletEip712Signer(walletClient);
const signature = await eip712signer.signAdd({
  owner: account,
  keyType: 1,
  key: publicKey,
  metadataType: 1,
  metadata,
  nonce,
  deadline,
});
```

```ts [Viem]
import { KEY_GATEWAY_EIP_712_TYPES } from '@farcaster/hub-web';
import { bytesToHex } from 'viem';
import { walletClient, account } from './clients.ts';
import { getPublicKey } from './signer.ts';
import { getMetadata } from './metadata.ts';
import { readNonce, getDeadline } from './helpers.ts';

const publicKey = await getPublicKey();
const metadata = await getMetadata();
const nonce = await readNonce();
const deadline = getDeadline();

const signature = await walletClient.signTypedData({
  account,
  ...KEY_GATEWAY_EIP_712_TYPES,
  primaryType: 'Add',
  message: {
    owner: account,
    keyType: 1,
    key: bytesToHex(publicKey),
    metadataType: 1,
    metadata,
    nonce,
    deadline,
  },
});
```

```ts [helpers.ts]
import { KEY_GATEWAY_ADDRESS, keyGatewayABI } from '@farcaster/hub-web';
import { publicClient, account } from './clients.ts';

export const getDeadline = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHour = 60 * 60;
  return now + oneHour;
};

export const readNonce = async () => {
  return await publicClient.readContract({
    address: KEY_GATEWAY_ADDRESS,
    abi: keyGatewayABI,
    functionName: 'nonces',
    args: [account],
  });
};
```

<<< @/examples/contracts/metadata.ts

<<< @/examples/contracts/signer.ts

<<< @/examples/contracts/clients.ts

:::

## Errores

| Error            | Selector   | Descripción                                                                                               |
| ---------------- | ---------- | --------------------------------------------------------------------------------------------------------- |
| InvalidMetadata  | `bcecb64a` | Los metadatos firmados proporcionados con la clave son inválidos.                                         |
| InvalidSignature | `8baa579f` | La firma proporcionada es inválida. Puede estar mal formateada o firmada por una dirección incorrecta.    |
| SignatureExpired | `0819bdcd` | La firma proporcionada ha expirado. Obtén una nueva firma del firmador con una marca de tiempo posterior. |

## Fuente

[`KeyGateway.sol`](https://github.com/farcasterxyz/contracts/blob/1aceebe916de446f69b98ba1745a42f071785730/src/KeyGateway.sol)
