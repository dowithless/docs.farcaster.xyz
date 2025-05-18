---
outline: [2, 3]
---

# Registro de Claves

El Registro de Claves almacena las claves públicas asociadas a cada cuenta de Farcaster.

Si deseas leer información sobre las claves de una cuenta de Farcaster o eliminar una clave existente, utiliza el Registro de Claves.

Si deseas agregar una nueva clave, utiliza en su lugar la [Puerta de Enlace de Claves](/es/reference/contracts/reference/key-gateway.md).

## Lectura

### totalKeys

Obtiene el número de claves activas (`uint256`) para un fid.

| Parámetro | tipo                                       | Descripción                              |
| --------- | ------------------------------------------ | ---------------------------------------- |
| fid       | `uint256`                                  | fid a consultar                          |
| state     | `uint8` (1 para Añadido, 2 para Eliminado) | Estado de la clave (añadida o eliminada) |

### keysOf

Enumera todas las claves públicas (`bytes[]`) para un fid.

| Parámetro | tipo                                       | Descripción                              |
| --------- | ------------------------------------------ | ---------------------------------------- |
| fid       | `uint256`                                  | fid a consultar                          |
| state     | `uint8` (1 para Añadido, 2 para Eliminado) | Estado de la clave (añadida o eliminada) |
| startIdx  | `uint256` (opcional)                       | Índice inicial para paginación           |
| batchSize | `uint256` (opcional)                       | Tamaño del lote para paginación          |

::: warning
¡No llames esto en cadena! Esta función consume mucho gas. Está diseñada para ser llamada por herramientas fuera de cadena, no por otros contratos.
:::

### keyDataOf

Devuelve el estado (`uint8`) y el tipo de clave (`uint32`) de una clave específica para un fid.

| Parámetro | tipo      | Descripción               |
| --------- | --------- | ------------------------- |
| fid       | `uint256` | fid a consultar           |
| key       | `bytes`   | clave pública a verificar |

## Escritura

### add

Revertirá si se llama directamente. Debe ser llamado a través de la [Puerta de Enlace de Claves](/es/reference/contracts/reference/key-gateway.md)

### remove

Elimina una clave pública del fid del llamador y la marca como `Eliminada`.

| Parámetro | tipo    | Descripción              |
| --------- | ------- | ------------------------ |
| key       | `bytes` | clave pública a eliminar |

::: warning
Eliminar una clave borrará todos los mensajes fuera de cadena asociados con la clave de los Hubs.
:::

### removeFor

Elimina una clave en nombre de otro fid proporcionando una firma. El propietario del fid debe firmar un mensaje EIP-712 `Remove` aprobando la eliminación. Revertirá si la clave no existe o ya ha sido eliminada.

| Parámetro | tipo      | Descripción                       |
| --------- | --------- | --------------------------------- |
| fidOwner  | `address` | dirección del propietario del fid |
| key       | `bytes`   | clave pública a eliminar          |
| deadline  | `uint256` | plazo de la firma                 |
| sig       | `bytes`   | firma EIP-712 del `fidOwner`      |

::: warning
Eliminar una clave borrará todos los mensajes fuera de cadena asociados con la clave de los Hubs.
:::

#### Firma Remove

Para eliminar una clave en nombre de otra cuenta, debes proporcionar una firma tipada EIP-712 de la cuenta en el siguiente formato:

`Remove(address owner,bytes key,uint256 nonce,uint256 deadline)`

| Parámetro | tipo      | Descripción                                                                          |
| --------- | --------- | ------------------------------------------------------------------------------------ |
| owner     | `address` | Dirección que posee el fid. El mensaje tipado debe estar firmado por esta dirección. |
| key       | `bytes`   | La clave pública a eliminar                                                          |
| nonce     | `uint256` | Nonce actual de la dirección `owner`                                                 |
| deadline  | `uint256` | Marca de tiempo de expiración de la firma                                            |

::: code-group

```ts [@farcaster/hub-web]
import { ViemWalletEip712Signer } from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { getPublicKey } from './signer.ts';
import { readNonce, getDeadline } from './helpers.ts';

const publicKey = await getPublicKey();
const nonce = await readNonce();
const deadline = getDeadline();

const eip712Signer = new ViemWalletEip712Signer(walletClient);
const signature = await eip712signer.signRemove({
  owner: account,
  key: publicKey,
  nonce,
  deadline,
});
```

```ts [Viem]
import { KEY_REGISTRY_EIP_712_TYPES } from '@farcaster/hub-web';
import { bytesToHex } from 'viem';
import { walletClient, account } from './clients.ts';
import { getPublicKey } from './signer.ts';
import { readNonce, getDeadline } from './helpers.ts';

const publicKey = await getPublicKey();
const nonce = await readNonce();
const deadline = getDeadline();

const signature = await walletClient.signTypedData({
  account,
  ...KEY_REGISTRY_EIP_712_TYPES,
  primaryType: 'Remove',
  message: {
    owner: account,
    key: bytesToHex(publicKey),
    nonce,
    deadline,
  },
});
```

```ts [helpers.ts]
import { KEY_REGISTRY_ADDRESS, keyRegistryABI } from '@farcaster/hub-web';
import { publicClient, account } from './clients.ts';

export const getDeadline = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHour = 60 * 60;
  return now + oneHour;
};

export const readNonce = async () => {
  return await publicClient.readContract({
    address: KEY_REGISTRY_ADDRESS,
    abi: keyRegistryABI,
    functionName: 'nonces',
    args: [account],
  });
};
```

<<< @/examples/contracts/signer.ts

<<< @/examples/contracts/clients.ts

:::

## Errores

| Error            | Selector   | Descripción                                                                                                                                                    |
| ---------------- | ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ExceedsMaximum   | `29264042` | Añadir la clave excede el número máximo de claves permitidas por fid (actualmente 1000)                                                                        |
| InvalidSignature | `8baa579f` | La firma proporcionada es inválida. Puede estar mal formateada o firmada por la dirección incorrecta.                                                          |
| InvalidState     | `baf3f0f7` | La acción viola las reglas de transición de estado. (Añadir una clave que ya existe, eliminar una clave que no existe, añadir una clave que ha sido eliminada) |
| SignatureExpired | `0819bdcd` | La firma proporcionada ha expirado. Obtén una nueva firma del firmante con una marca de tiempo de plazo posterior.                                             |

## Fuente

[`KeyRegistry.sol`](https://github.com/farcasterxyz/contracts/blob/1aceebe916de446f69b98ba1745a42f071785730/src/KeyRegistry.sol)
