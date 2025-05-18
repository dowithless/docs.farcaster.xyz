# Crear una clave de cuenta

Una cuenta puede autorizar claves de cuenta, las cuales pueden crear mensajes en su nombre.

El propietario de la cuenta puede revocar una clave de cuenta en cualquier momento. Para agregar una clave de cuenta, deberás seguir seis pasos:

1. Configurar clientes [Viem](https://viem.sh/) y la clave de cuenta [`@farcaster/hub-web`](https://www.npmjs.com/package/@farcaster/hub-web).
2. Registrar un [app FID](/es/reference/contracts/faq#what-is-an-app-fid-how-do-i-get-one) si tu aplicación aún no tiene uno.
3. Crear una nueva clave de cuenta para el usuario.
4. Usar tu cuenta de aplicación para crear una [Solicitud de Clave Firmada](/es/reference/contracts/reference/signed-key-request-validator).
5. Recoger una firma [`Add`](/es/reference/contracts/reference/key-gateway#add-signature) del usuario.
6. Llamar al contrato [Key Gateway](https://docs.farcaster.xyz/reference/contracts/reference/key-gateway#addFor) para agregar la clave en la cadena.

### Requisitos

- Una billetera ETH en OP Mainnet, con algo de ETH.
- Una URL RPC de ETH para OP Mainnet (por ejemplo, a través de [Alchemy](https://www.alchemy.com/), [Infura](https://www.infura.io/) o [QuickNode](https://www.quicknode.com/)).

### 1. Configurar clientes y clave de cuenta

Primero, configura los clientes Viem y la clave de cuenta `@farcaster/hub-web`. En este ejemplo, usaremos cuentas locales de Viem y clave de cuenta, pero también puedes usar `ViemWalletEip712Signer` para conectarte a la billetera de un usuario en lugar de una cuenta local.

```ts
import * as ed from '@noble/ed25519';
import {
  ID_GATEWAY_ADDRESS,
  ID_REGISTRY_ADDRESS,
  ViemLocalEip712Signer,
  idGatewayABI,
  idRegistryABI,
  NobleEd25519Signer,
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

Registra un app FID si aún no tienes uno. Para registrar un FID, necesitarás leer el precio del ID Gateway, luego llamar al ID Gateway y pagar el precio de registro. Puedes leer tu nuevo FID del contrato Id Registry, o analizarlo desde un evento `Register`. Aquí, lo leeremos del contrato de registro.

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

### 3. Crear una nueva clave de cuenta

Crea un nuevo par de claves Ed25519 para el usuario. En una aplicación real, asegúrate de mantener la clave privada en secreto.

```ts
const privateKeyBytes = ed.utils.randomPrivateKey();
const accountKey = new NobleEd25519Signer(privateKeyBytes);

let accountPubKey = new Uint8Array();
const accountKeyResult = await accountKey.getSignerKey();
```

### 4. Usar tu cuenta de aplicación para crear una Solicitud de Clave Firmada

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

### 5. Recoger una firma `Add` del usuario

Recoge una firma EIP-712 `Add` del usuario para autorizar agregar una clave de cuenta a su FID. En una aplicación real, probablemente recogerás esta firma en tu frontend, desde la billetera del usuario. En un contexto de frontend, puedes usar un `ViemEip712WalletSigner` para conectarte a una billetera de navegador en lugar de un firmante local.

```ts
if (signedKeyRequestMetadata.isOk()) {
  const metadata = bytesToHex(signedKeyRequestMetadata.value);

  const aliceNonce = await publicClient.readContract({
    address: KEY_GATEWAY_ADDRESS,
    abi: keyGatewayABI,
    functionName: 'nonces',
    args: [alice.address],
  });

  const aliceSignature = await aliceAccountKey.signAdd({
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

### 6. Llamar al contrato Key Gateway para agregar la clave en la cadena

Llama al contrato Key Gateway y proporciona la firma del usuario para agregar la clave en la cadena.

```ts
if (aliceSignature.isOk()) {
  const { request } = await publicClient.simulateContract({
    account: app,
    address: KEY_GATEWAY_ADDRESS,
    abi: keyGatewayABI,
    functionName: 'addFor',
    args: [
      alice.address,
      1,
      bytesToHex(accountPubKey),
      1,
      metadata,
      deadline,
      bytesToHex(aliceSignature.value),
    ],
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
  KEY_GATEWAY_ADDRESS,
  keyGatewayABI,
} from '@farcaster/hub-nodejs';
import { bytesToHex, createPublicClient, createWalletClient, http } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { optimism } from 'viem/chains';

const APP_PRIVATE_KEY = '0x00';
const ALICE_PRIVATE_KEY = '0x00';

/*******************************************************************************
 * Configuración - Crear cuentas locales, clientes Viem, ayudantes y constantes.
 *******************************************************************************/

/**
 * Crear clientes públicos (lectura) y de billetera (escritura) de Viem.
 */
const publicClient = createPublicClient({
  chain: optimism,
  transport: http(),
});

const walletClient = createWalletClient({
  chain: optimism,
  transport: http(),
});

/**
 * Una cuenta local que representa tu aplicación. La usarás
 * para firmar metadatos de clave y enviar
 * transacciones en nombre de los usuarios.
 */
const app = privateKeyToAccount(APP_PRIVATE_KEY);
const appAccountKey = new ViemLocalEip712Signer(app as any);
console.log('App:', app.address);

/**
 * Una cuenta local que representa a Alice, una usuaria.
 */
const alice = privateKeyToAccount(ALICE_PRIVATE_KEY);
const aliceAccountKey = new ViemLocalEip712Signer(alice as any);
console.log('Alice:', alice.address);

/**
 * Generar un plazo de tiempo una hora a partir de ahora.
 * Todas las firmas EIP-712 de Farcaster incluyen un plazo, un timestamp de bloque
 * después del cual la firma ya no es válida.
 */
const deadline = BigInt(Math.floor(Date.now() / 1000) + 3600); // Establece el plazo de las firmas a 1 hora desde ahora

const WARPCAST_RECOVERY_PROXY = '0x00000000FcB080a4D6c39a9354dA9EB9bC104cd7';

/*******************************************************************************
 * IdGateway - register - Registrar un app FID.
 *******************************************************************************/

/**
 *  Obtener el precio actual para registrar. No vamos a registrar
 *  almacenamiento adicional, así que pasamos 0n como único argumento.
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
 *  Leer el app FID del contrato Id Registry.
 */
const APP_FID = await publicClient.readContract({
  address: ID_REGISTRY_ADDRESS,
  abi: idRegistryABI,
  functionName: 'idOf',
  args: [app.address],
});

/*******************************************************************************
 * KeyGateway - addFor - Agregar una clave de cuenta al FID de Alice.
 *******************************************************************************/

/**
 * Para agregar una clave de cuenta al FID de Alice, necesitamos seguir cuatro pasos:
 *
 * 1. Crear un nuevo par de claves de cuenta para Alice.
 * 2. Usar nuestra cuenta de aplicación para crear una Solicitud de Clave Firmada.
 * 3. Recoger la firma `Add` de Alice.
 * 4. Llamar al contrato para agregar la clave en la cadena.
 */

/**
 *  1. Crear un par de claves de cuenta Ed25519 para Alice y obtener la clave pública.
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
     *  3. Leer el nonce de Alice del Key Gateway.
     */
    const aliceNonce = await publicClient.readContract({
      address: KEY_GATEWAY_ADDRESS,
      abi: keyGatewayABI,
      functionName: 'nonces',
      args: [alice.address],
    });

    /**
     *  Luego, recoger su firma `Add`.
     */
    const aliceSignature = await aliceAccountKey.signAdd({
      owner: alice.address as `0x${string}`,
      keyType: 1,
      key: accountPubKey,
      metadataType: 1,
      metadata,
      nonce: aliceNonce,
      deadline,
    });

    if (aliceSignature.isOk()) {
      /**
       *  Llamar a `addFor` con la firma de Alice y la solicitud de clave firmada.
       */
      const { request } = await publicClient.simulateContract({
        account: app,
        address: KEY_GATEWAY_ADDRESS,
        abi: keyGatewayABI,
        functionName: 'addFor',
        args: [
          alice.address,
          1,
          bytesToHex(accountPubKey),
          1,
          metadata,
          deadline,
          bytesToHex(aliceSignature.value),
        ],
      });
      await walletClient.writeContract(request);
    }
  }
}
```

:::

Consulta la referencia de [Key Registry](/es/reference/contracts/reference/key-registry#add) para más detalles.
