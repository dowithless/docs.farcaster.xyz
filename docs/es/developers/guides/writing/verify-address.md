# Crear una verificación de dirección

::: info Requisitos previos

- Acceso de escritura a una instancia de hubble
- Clave privada de una cuenta registrada a un fid
- Una URL de proveedor de Ethereum para OP Mainnet (por ejemplo, a través de [Alchemy](https://www.alchemy.com/), [Infura](https://www.infura.io/) o [QuickNode](https://www.quicknode.com/)).

:::

Para crear una [Verificación](https://docs.farcaster.xyz/reference/hubble/datatypes/messages.html#_6-verification) que demuestre la propiedad de una dirección externa de Ethereum, puedes usar un `Eip712Signer` para firmar un mensaje de verificación, y la función `makeVerificationAddEthAddress` para construir un mensaje que enviar a un Hub.

Primero, configura los clientes y las claves de cuenta:

```ts
import {
  NobleEd25519Signer,
  ViemLocalEip712Signer,
  FarcasterNetwork,
  makeVerificationAddEthAddress,
} from '@farcaster/hub-nodejs';
import { createPublicClient, http } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { optimism } from 'viem/chains';

const publicClient = createPublicClient({
  chain: optimism,
  transport: http(),
});

const alice = privateKeyToAccount('0xab..12');
const eip712Signer = new ViemLocalEip712Signer(alice);

const ed25519Signer = new NobleEd25519Signer('0xcd..34');
```

Luego crea una firma de verificación usando un `Eip712Signer`. Necesitarás consultar el último hash de bloque en OP mainnet e incluirlo en el mensaje firmado.

```ts
const latestBlock = await publicClient.getBlock();

const fid = 1n;
const network = FarcasterNetwork.MAINNET;
const address = alice.address;
const blockHash = latestBlock.blockhash;

const ethSignature = eip712Signer.signVerificationEthAddressClaim({
  fid,
  address,
  network,
  blockHash,
});
```

Finalmente, usa `makeVerificationAddEthAddress` para construir un mensaje de [`Verificación`](https://docs.farcaster.xyz/reference/hubble/datatypes/messages.html#_6-verification) que puedas enviar a un Hub.

```ts
if (ethSignature.isOk()) {
  const message = await makeVerificationAddEthAddress(
    {
      address,
      blockHash,
      ethSignature,
    },
    { fid, network },
    ed25519Signer
  );
}
```

### Ejemplo de código completo

::: code-group

```ts [@farcaster/hub-nodejs]
import {
  NobleEd25519Signer,
  ViemLocalEip712Signer,
  FarcasterNetwork,
  makeVerificationAddEthAddress,
} from '@farcaster/hub-nodejs';
import { createPublicClient, http } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { optimism } from 'viem/chains';

const publicClient = createPublicClient({
  chain: optimism,
  transport: http(),
});

const alice = privateKeyToAccount('0xab..12');
const eip712Signer = new ViemLocalEip712Signer(alice);

const ed25519Signer = new NobleEd25519Signer('0xcd..34');

const latestBlock = await publicClient.getBlock();

const fid = 1n;
const network = FarcasterNetwork.MAINNET;
const address = alice.address;
const blockHash = latestBlock.blockhash;

const ethSignature = eip712Signer.signVerificationEthAddressClaim({
  fid,
  address,
  network,
  blockHash,
});

if (ethSignature.isOk()) {
  const message = await makeVerificationAddEthAddress(
    {
      address,
      blockHash,
      ethSignature,
    },
    { fid, network },
    ed25519Signer
  );
}
```

:::
