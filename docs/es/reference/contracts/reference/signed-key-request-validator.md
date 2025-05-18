---
outline: [2, 3]
---

# Validador de Solicitud de Clave Firmada

El Validador de Solicitud de Clave Firmada verifica los metadatos firmados asociados con las claves. El Registro de Claves llama al validador antes de agregar una clave, para comprobar que la Solicitud de Clave Firmada proporcionada es válida.

Si deseas construir o verificar una Solicitud de Clave Firmada, utiliza el Validador de Solicitud de Clave Firmada.

::: info ¿Qué es una Solicitud de Clave Firmada?

Cuando un usuario agrega una clave a su cuenta (el fid principal), debe incluir una firma de la persona que la solicitó (el fid solicitante). Esto permite que cualquiera pueda identificar quién solicitó una clave específica.

Normalmente, el fid principal es el usuario final y el fid solicitante es una app a la que el usuario desea conectarse.
:::

## Lectura

### encodeMetadata

Convierte una estructura [`SignedKeyRequestMetadata`](#signedkeyrequestmetadata-struct) en `bytes` para pasarla a funciones de contrato como [add](/es/reference/contracts/reference/key-gateway#add), [register](/es/reference/contracts/reference/bundler#register).

| Parámetro | tipo                       | Descripción                                          |
| --------- | -------------------------- | ---------------------------------------------------- |
| metadata  | `SignedKeyRequestMetadata` | La estructura `SignedKeyRequestMetadata` a codificar |

#### Estructura SignedKeyRequestMetadata

La estructura `SignedKeyRequestMetadata` contiene datos para validar la autenticidad de una Solicitud de Clave Firmada: fid solicitante, el propietario del fid solicitante y una firma EIP-712 [`SignedKeyRequest`](#signedkeyrequest-signature).

| Parámetro     | tipo      | Descripción                                                                                        |
| ------------- | --------- | -------------------------------------------------------------------------------------------------- |
| requestFid    | `uint256` | fid solicitante                                                                                    |
| requestSigner | `address` | dirección del propietario del fid solicitante                                                      |
| signature     | `bytes`   | Firma EIP-712 [`SignedKeyRequest`](#signedkeyrequest-signature) desde la dirección `requestSigner` |
| deadline      | `uint256` | Marca de tiempo de expiración de la firma                                                          |

A continuación se muestran ejemplos de código que demuestran dos métodos para generar `SignedKeyRequestMetadata` codificada — usando la biblioteca `@farcaster/hub-web` para firmar y codificar en un solo paso, o usando Viem para firmar y codificar por separado.

::: code-group

```ts [@farcaster/hub-web]
import { ViemLocalEip712Signer } from '@farcaster/hub-web';
import { privateKeyToAccount } from 'viem/accounts';
import { getDeadline } from './helpers.ts';
import { getPublicKey } from './signer.ts';

export const appAccount = privateKeyToAccount('0x...');

const key = getPublicKey();
const deadline = getDeadline();

// El helper getSignedKeyRequestMetadata genera una Solicitud de Clave Firmada
// y devuelve una estructura SignedKeyRequest codificada en ABI.
const eip712Signer = new ViemLocalEip712Signer(appAccount);
const encodedData = await eip712Signer.getSignedKeyRequestMetadata({
  requestFid: 9152n,
  key,
  deadline,
});
```

```ts [Viem]
import { bytesToHex, encodeAbiParameters } from 'viem';
import { signature } from './signature.ts';
import { getDeadline } from './helpers.ts';

const deadline = getDeadline();

// Un ejemplo de recopilar la firma y
// codificar los metadatos de SignedKeyRequest por separado.
const encodedData = encodeAbiParameters(
  [
    {
      components: [
        {
          name: 'requestFid',
          type: 'uint256',
        },
        {
          name: 'requestSigner',
          type: 'address',
        },
        {
          name: 'signature',
          type: 'bytes',
        },
        {
          name: 'deadline',
          type: 'uint256',
        },
      ],
      type: 'tuple',
    },
  ],
  [
    {
      requestFid: 9152n,
      requestSigner: '0x02ef790dd7993a35fd847c053eddae940d055596',
      bytesToHex(signature),
      deadline,
    },
  ]
);
```

```ts [signature.ts]
import { ViemLocalEip712Signer } from '@farcaster/hub-web';
import { privateKeyToAccount } from 'viem/accounts';
import { getDeadline } from './helpers.ts';
import { getPublicKey } from './signer.ts';

export const appAccount = privateKeyToAccount('0x...');

const key = getPublicKey();
const deadline = getDeadline();

const eip712Signer = new ViemLocalEip712Signer(appAccount);
const signature = await eip712Signer.signKeyRequest({
  requestFid: 9152n,
  key,
  deadline,
});
```

```ts [helpers.ts]
export const getDeadline = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHour = 60 * 60;
  return now + oneHour;
};
```

<<< @/examples/contracts/signer.ts

:::

### validate

Valida una estructura [`SignedKeyRequestMetadata`](#signedkeyrequestmetadata-struct) codificada. El Registro de Claves llama a esta función internamente cuando un usuario agrega una clave para validar la Solicitud de Clave Firmada. Si estás creando claves en nombre de usuarios, puedes llamar a esta función tú mismo para validar la Solicitud de Clave Firmada creada por tu app.

| Parámetro | tipo      | Descripción                                                          |
| --------- | --------- | -------------------------------------------------------------------- |
| fid       | `uint256` | Fid principal que se asociará con la clave                           |
| key       | `bytes`   | Bytes de la clave pública a validar                                  |
| sig       | `bytes`   | Firma EIP-712 `SignedKeyRequest` de la entidad que solicita la clave |

#### Firma SignedKeyRequest

El mensaje `SignedKeyRequest` es una firma tipada EIP-712 firmada por el propietario del fid solicitante en el siguiente formato:

`SignedKeyRequest(uint256 requestFid,bytes key,uint256 deadline)`

::: info ¿Por qué firmar metadatos?
La firma `SignedKeyRequest` prueba que el fid solicitante pidió al fid principal autorizar un par de claves. Por ejemplo, cuando una app le pide a un usuario que agregue una nueva clave, la app crea una Solicitud de Clave Firmada que prueba que hizo la solicitud y el Registro de Claves la emite en un evento onchain. Esto permite que cualquiera pueda atribuir un firmante a la persona específica que lo solicitó, lo cual es útil para una amplia gama de cosas, desde saber qué apps se están usando hasta filtrar contenido basado en las aplicaciones que lo generaron.
:::

| Parámetro  | tipo      | Descripción                               |
| ---------- | --------- | ----------------------------------------- |
| requestFid | `uint256` | fid de la entidad                         |
| key        | `bytes`   | Bytes de la clave pública                 |
| deadline   | `uint256` | Marca de tiempo de expiración de la firma |

::: code-group

```ts [@farcaster/hub-web]
import { ViemLocalEip712Signer } from '@farcaster/hub-web';
import { privateKeyToAccount } from 'viem/accounts';
import { getDeadline } from './helpers.ts';
import { getPublicKey } from './signer.ts';

export const appAccount = privateKeyToAccount('0x...');

const key = getPublicKey();
const deadline = getDeadline();

const eip712Signer = new ViemLocalEip712Signer(appAccount);
const signature = await eip712Signer.signKeyRequest({
  requestFid: 9152n,
  key,
  deadline,
});
```

```ts [Viem]
import { SIGNED_KEY_REQUEST_VALIDATOR_EIP_712_TYPES } from '@farcaster/hub-web';
import { bytesToHex, privateKeyToAccount } from 'viem/accounts';
import { getDeadline } from './helpers.ts';
import { getPublicKey } from './signer.ts';

export const appAccount = privateKeyToAccount('0x...');

const key = getPublicKey();
const deadline = getDeadline();

const signature = await appAccount.signTypedData({
  ...SIGNED_KEY_REQUEST_VALIDATOR_EIP_712_TYPES,
  primaryType: 'SignedKeyRequest',
  message: {
    requestFid: 9152n,
    key: bytesToHex(key),
    deadline,
  },
});
```

```ts [helpers.ts]
export const getDeadline = () => {
  const now = Math.floor(Date.now() / 1000);
  const oneHour = 60 * 60;
  return now + oneHour;
};
```

<<< @/examples/contracts/signer.ts

:::

## Fuente

[`SignedKeyRequestValidator.sol`](https://github.com/farcasterxyz/contracts/blob/1aceebe916de446f69b98ba1745a42f071785730/src/validators/SignedKeyRequestValidator.sol)
