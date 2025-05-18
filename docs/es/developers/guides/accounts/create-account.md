# Crear una cuenta

::: info Requisitos previos

- Una billetera Ethereum en OP Mainnet, con suficiente ETH para cubrir los costos de gas.
- Una URL de proveedor Ethereum para OP Mainnet (por ejemplo, a través de [Alchemy](https://www.alchemy.com/), [Infura](https://www.infura.io/) o [QuickNode](https://www.quicknode.com/)).

:::

Puedes registrar un nuevo usuario utilizando el contrato Bundler. Para hacerlo, necesitarás:

1. Configurar clientes [Viem](https://viem.sh/) y claves de cuenta [`@farcaster/hub-web`](https://www.npmjs.com/package/@farcaster/hub-web).
2. Registrar un [app FID](/es/reference/contracts/faq#what-is-an-app-fid-how-do-i-get-one) si tu aplicación aún no tiene uno.
3. Obtener una firma [`Register`](/es/reference/contracts/reference/id-gateway#register-signature) del usuario.
4. Crear un nuevo par de claves de cuenta para el usuario.
5. Usar tu cuenta de aplicación para crear una [Solicitud de Clave Firmada](/es/reference/contracts/reference/signed-key-request-validator).
6. Obtener una firma [`Add`](/es/reference/contracts/reference/key-gateway#add-signature) del usuario.
7. Llamar al contrato [Bundler](https://docs.farcaster.xyz/reference/contracts/reference/bundler#register) para registrar en la cadena.

### 1. Configurar clientes y claves de cuenta

Primero, configura los clientes Viem y las claves de cuenta `@farcaster/hub-web`. En este ejemplo, usaremos cuentas locales de Viem y claves de cuenta, pero también puedes usar `ViemWalletEip712Signer` para conectarte a la billetera de un usuario en lugar de una cuenta local.

```ts
import * as ed from '@noble/ed25519';
import {
  ID_GATEWAY_ADDRESS,
  ID_REGISTRY_ADDRESS,
  ViemLocalEip712Signer,
  idGatewayABI,
  idRegistryABI,
  NobleEd25519Signer,
  BUNDLER_ADDRESS,
  bundlerABI,
  KEY_GATEWAY_ADDRESS,
  keyGatewayABI,
} from '@farcaster/hub-nodejs';
import { bytesToHex, createPublicClient, createWalletClient, http } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { optimism } from 'viem/chains';

const APP_PRIVATE_KEY = '0x00';
const ALICE_PRIVATE_KEY = '0x00';

const publicClient = createPublicClient({
  chain: optimism,
  transport: http(),
});

const walletClient = createWalletClient({
  chain: optimism,
  transport: http(),
});

const app = privateKeyToAccount(APP_PRIVATE_KEY);
const appAccountKey = new ViemLocalEip712Signer(app as any);

const alice = privateKeyToAccount(ALICE_PRIVATE_KEY);
const aliceAccountKey = new ViemLocalEip712Signer(alice as any);

const deadline = BigInt(Math.floor(Date.now() / 1000) + 3600); // Establece el plazo de las firmas a 1 hora desde ahora

const WARPCAST_RECOVERY_PROXY = '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7';
```

### 2. Registrar un app FID

Registra un app FID si aún no tienes uno. Para registrar un FID, necesitarás leer el precio desde el ID Gateway, luego llamar al ID Gateway y pagar el precio de registro. Puedes leer tu nuevo FID desde el contrato Id Registry, o analizarlo desde un evento `Register`. Aquí, lo leeremos desde el contrato de registro.

```ts
const price = await publicClient.readContract({
  address: ID_GATEWAY_ADDRESS,
  abi: idGatewayABI,
  functionName: 'price',
  args: [0n],
});

const { request } = await publicClient.simulateContract({
  account: app,
  address: ID_GATEWAY_ADDRESS,
  abi: idGatewayABI,
  functionName: 'register',
  args: [WARPCAST_RECOVERY_PROXY, 0n],
  value: price,
});
await walletClient.writeContract(request);

const APP_FID = await publicClient.readContract({
  address: ID_REGISTRY_ADDRESS,
  abi: idRegistryABI,
  functionName: 'idOf',
  args: [app.address],
});
```

### 3. Obtener una firma `Register` del usuario

Obtén una firma EIP-712 `Register` del usuario. En una aplicación real, probablemente obtendrás esta firma en tu frontend, desde la billetera del usuario. En un contexto de frontend, puedes usar un `ViemEip712WalletSigner` para conectarte a una billetera del navegador en lugar de un firmante local.

```ts
let nonce = await publicClient.readContract({
  address: ID_REGISTRY_ADDRESS,
  abi: idRegistryABI,
  functionName: 'nonces',
  args: [alice.address],
});

const registerSignatureResult = await aliceAccountKey.signRegister({
  to: alice.address as `0x${string}`,
  recovery: WARPCAST_RECOVERY_PROXY,
  nonce,
  deadline,
});

let registerSignature;
if (registerSignatureResult.isOk()) {
  registerSignature = registerSignatureResult.value;
} else {
  throw new Error('Failed to generate register signature');
}
```

### 4. Crear un nuevo par de claves de cuenta

Crea un nuevo par de claves Ed25519 para el usuario. En una aplicación real, asegúrate de mantener la clave privada del usuario en secreto.

```ts
const privateKeyBytes = ed.utils.randomPrivateKey();
const accountKey = new NobleEd25519Signer(privateKeyBytes);

let accountPubKey = new Uint8Array();
const accountKeyResult = await accountKey.getSignerKey();
```

### 5. Usar tu cuenta de aplicación para crear una Solicitud de Clave Firmada

Crea una Solicitud de Clave Firmada, firmada por tu cuenta de aplicación. Para hacerlo, puedes usar el ayudante `getSignedKeyRequestMetadata`, que genera y firma la Solicitud de Clave Firmada.

```ts
if (accountKeyResult.isOk()) {
  accountPubKey = accountKeyResult.value;

  const signedKeyRequestMetadata =
    await appAccountKey.getSignedKeyRequestMetadata({
      requestFid: APP_FID,
      key: accountPubKey,
      deadline,
    });
}
```

### 6. Obtener una firma `Add` del usuario

Obtén una firma EIP-712 `Add` del usuario para autorizar agregar una clave de cuenta a su FID.

```ts
if (signedKeyRequestMetadata.isOk()) {
  const metadata = bytesToHex(signedKeyRequestMetadata.value);

  nonce = await publicClient.readContract({
    address: KEY_GATEWAY_ADDRESS,
    abi: keyGatewayABI,
    functionName: 'nonces',
    args: [alice.address],
  });

  const addSignatureResult = await aliceAccountKey.signAdd({
    owner: alice.address as `0x${string}`,
    keyType: 1,
    key: accountPubKey,
    metadataType: 1,
    metadata,
    nonce,
    deadline,
  });
}
```

### 7. Llamar al contrato Bundler para registrar en la cadena

Llama al contrato Key Gateway y proporciona la firma del usuario para agregar la clave en la cadena.

```ts
if (addSignatureResult.isOk()) {
  const addSignature = addSignatureResult.value;

  const price = await publicClient.readContract({
    address: BUNDLER_ADDRESS,
    abi: bundlerABI,
    functionName: 'price',
    args: [0n],
  });

  const { request } = await publicClient.simulateContract({
    account: app,
    address: BUNDLER_ADDRESS,
    abi: bundlerABI,
    functionName: 'register',
    args: [
      {
        to: alice.address,
        recovery: WARPCAST_RECOVERY_PROXY,
        sig: bytesToHex(registerSignature),
        deadline,
      },
      [
        {
          keyType: 1,
          key: bytesToHex(accountPubKey),
          metadataType: 1,
          metadata: metadata,
          sig: bytesToHex(addSignature.value),
          deadline,
        },
      ],
      0n,
    ],
    value: price,
  });
  await walletClient.writeContract(request);
}
```

### Ejemplo de código completo

Consulta el ejemplo de código completo a continuación para todos los pasos anteriores.

::: code-group

```ts [@farcaster/hub-web]
import * as ed from '@noble/ed25519';
import {
  ID_GATEWAY_ADDRESS,
  ID_REGISTRY_ADDRESS,
  ViemLocalEip712Signer,
  idGatewayABI,
  idRegistryABI,
  NobleEd25519Signer,
  BUNDLER_ADDRESS,
  bundlerABI,
  KEY_GATEWAY_ADDRESS,
  keyGatewayABI,
} from '@farcaster/hub-nodejs';
import { bytesToHex, createPublicClient, createWalletClient, http } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { optimism } from 'viem/chains';

const APP_PRIVATE_KEY = '0x00';
const ALICE_PRIVATE_KEY = '0x00';

const publicClient = createPublicClient({
  chain: optimism,
  transport: http('http://localhost:8545'),
});

const walletClient = createWalletClient({
  chain: optimism,
  transport: http('http://localhost:8545'),
});

const app = privateKeyToAccount(APP_PRIVATE_KEY);
const appAccountKey = new ViemLocalEip712Signer(app as any);

const alice = privateKeyToAccount(ALICE_PRIVATE_KEY);
const aliceAccountKey = new ViemLocalEip712Signer(alice as any);

const deadline = BigInt(Math.floor(Date.now() / 1000) + 3600); // Establece el plazo de las firmas a 1 hora desde ahora

const WARPCAST_RECOVERY_PROXY = '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7';

/*******************************************************************************
 * IdGateway - register - Registrar un app FID.
 *******************************************************************************/

/**
 *  Obtener el precio actual para registrar. No vamos a registrar ningún
 *  almacenamiento adicional, por lo que pasamos 0n como único argumento.
 */
const price = await publicClient.readContract({
  address: ID_GATEWAY_ADDRESS,
  abi: idGatewayABI,
  functionName: 'price',
  args: [0n],
});

/**
 *  Llamar a `register` para registrar un FID en la cuenta de la aplicación.
 */
const { request } = await publicClient.simulateContract({
  account: app,
  address: ID_GATEWAY_ADDRESS,
  abi: idGatewayABI,
  functionName: 'register',
  args: [WARPCAST_RECOVERY_PROXY, 0n],
  value: price,
});
await walletClient.writeContract(request);

/**
 *  Leer el app fid desde el contrato Id Registry.
 */
const APP_FID = await publicClient.readContract({
  address: ID_REGISTRY_ADDRESS,
  abi: idRegistryABI,
  functionName: 'idOf',
  args: [app.address],
});

/*******************************************************************************
 * Obtener firma Register de Alice
 *******************************************************************************/

let nonce = await publicClient.readContract({
  address: KEY_GATEWAY_ADDRESS,
  abi: keyGatewayABI,
  functionName: 'nonces',
  args: [alice.address],
});

const registerSignatureResult = await aliceAccountKey.signRegister({
  to: alice.address as `0x${string}`,
  recovery: WARPCAST_RECOVERY_PROXY,
  nonce,
  deadline,
});

let registerSignature;
if (registerSignatureResult.isOk()) {
  registerSignature = registerSignatureResult.value;
} else {
  throw new Error('Failed to generate register signature');
}

/*******************************************************************************
 * Obtener firma Add de Alice.
 *******************************************************************************/

/**
 *  1. Crear un par de claves Ed25519 para Alice y obtener la clave pública.
 */
const privateKeyBytes = ed.utils.randomPrivateKey();
const accountKey = new NobleEd25519Signer(privateKeyBytes);

let accountPubKey = new Uint8Array();
const accountKeyResult = await accountKey.getSignerKey();
if (accountKeyResult.isOk()) {
  accountPubKey = accountKeyResult.value;

  /**
   *  2. Generar una Solicitud de Clave Firmada desde la cuenta de la aplicación.
   */
  const signedKeyRequestMetadata =
    await appAccountKey.getSignedKeyRequestMetadata({
      requestFid: APP_FID,
      key: accountPubKey,
      deadline,
    });

  if (signedKeyRequestMetadata.isOk()) {
    const metadata = bytesToHex(signedKeyRequestMetadata.value);
    /**
     *  3. Leer el nonce de Alice desde el Key Gateway.
     */
    nonce = await publicClient.readContract({
      address: KEY_GATEWAY_ADDRESS,
      abi: keyGatewayABI,
      functionName: 'nonces',
      args: [alice.address],
    });

    /**
     *  Luego, obtener su firma `Add`.
     */
    const addSignatureResult = await aliceAccountKey.signAdd({
      owner: alice.address as `0x${string}`,
      keyType: 1,
      key: accountPubKey,
      metadataType: 1,
      metadata,
      nonce,
      deadline,
    });

    if (addSignatureResult.isOk()) {
      const addSignature = addSignatureResult.value;
      /**
       *  Leer el precio actual de registro.
       */
      const price = await publicClient.readContract({
        address: BUNDLER_ADDRESS,
        abi: bundlerABI,
        functionName: 'price',
        args: [0n],
      });

      /**
       *  Llamar a `register` con las firmas de Alice, registro y parámetros de clave.
       */
      const { request } = await publicClient.simulateContract({
        account: app,
        address: BUNDLER_ADDRESS,
        abi: bundlerABI,
        functionName: 'register',
        args: [
          {
            to: alice.address,
            recovery: WARPCAST_RECOVERY_PROXY,
            sig: bytesToHex(registerSignature),
            deadline,
          },
          [
            {
              keyType: 1,
              key: bytesToHex(accountPubKey),
              metadataType: 1,
              metadata: metadata,
              sig: bytesToHex(addSignature),
              deadline,
            },
          ],
          0n,
        ],
        value: price,
      });
      await walletClient.writeContract(request);
    }
  }
}
```

:::

Consulta la referencia de [Bundler](/es/reference/contracts/reference/bundler#register) para más detalles.
