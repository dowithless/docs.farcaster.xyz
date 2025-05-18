# Cambiar dirección de custodia

Las cuentas son propiedad de una dirección de custodia, que es una dirección de Ethereum en OP Mainnet.

Un usuario puede querer cambiar esta dirección por razones de seguridad o para transferir la propiedad de toda la cuenta.

### Requisitos

- Una billetera ETH que posea la cuenta en OP Mainnet, con algo de ETH.
- Una URL de proveedor de Ethereum para OP Mainnet (por ejemplo, a través de [Alchemy](https://www.alchemy.com/), [Infura](https://www.infura.io/) o [QuickNode](https://www.quicknode.com/)).

### Cambiar dirección de custodia

Llama a la función `transfer` en el contrato Id Registry. La dirección receptora debe proporcionar una firma EIP-712 aceptando la transferencia.

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

const { request: transferRequest } = await publicClient.simulateContract({
  ...IdContract,
  functionName: 'transfer',
  args: [account, deadline, signature], // to, deadline, signature
});

await walletClient.writeContract(transferRequest);
```

```ts [Viem]
import {
  ID_REGISTRY_EIP_712_TYPES,
  idRegistryABI,
  ID_GATEWAY_ADDRESS,
} from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';
import { readNonce, getDeadline } from './helpers.ts';

const nonce = await readNonce();
const deadline = getDeadline();
const IdContract = {
  abi: idRegistryABI,
  address: ID_GATEWAY_ADDRESS,
  chain: optimism,
};

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
    functionName: 'nonces',
    args: [account],
  });
};
```

<<< @/examples/contracts/clients.ts

:::

::: warning
Transferir un FID no restablece su dirección de recuperación. Para transferir un FID y actualizar su dirección de recuperación,
llama a [`transferAndChangeRecovery`](/es/reference/contracts/reference/id-registry#transferandchangerecovery).
:::

Consulta la sección [Id Registry](/es/reference/contracts/reference/id-registry#transfer) para más
detalles.
