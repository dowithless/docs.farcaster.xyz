# Cambiar dirección de recuperación

Las cuentas pueden configurar una dirección de recuperación para protegerse contra la pérdida de la dirección de custodia. La dirección de recuperación cambiará la dirección de custodia de la cuenta.

### Requisitos

- Una wallet de ETH que tenga un FID en OP Mainnet, con algo de ETH para cubrir los costos de gas.
- Una URL RPC de ETH para OP Mainnet (por ejemplo, a través de [Alchemy](https://www.alchemy.com/), [Infura](https://www.infura.io/) o [QuickNode](https://www.quicknode.com/)).

### Cambiar dirección

Llama a la función `changeRecovery` en el contrato Id Registry.

::: code-group

```ts [@farcaster/hub-web]
import { walletClient, account, IdContract } from './clients.ts';

const newRecoveryAddress = '0x...';

const { request: transferRequest } = await walletClient.simulateContract({
  ...IdContract,
  functionName: 'changeRecovery',
  args: [newRecoveryAddress], // Nueva dirección de recuperación
});

await walletClient.writeContract(transferRequest);
```

```ts [clients.ts]
import {
  ID_REGISTRY_EIP_712_TYPES,
  idRegistryABI,
  ID_GATEWAY_ADDRESS,
} from '@farcaster/hub-web';
import { walletClient, account } from './clients.ts';

const IdContract = {
  abi: idRegistryABI,
  address: ID_GATEWAY_ADDRESS,
  chain: optimism,
};

import { createWalletClient, createPublicClient, custom, http } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { optimism } from 'viem/chains';

export const publicClient = createPublicClient({
  chain: optimism,
  transport: http(),
});

export const walletClient = createWalletClient({
  chain: optimism,
  transport: custom(window.ethereum),
});

// Cuenta JSON-RPC
export const [account] = await walletClient.getAddresses();

// Cuenta local
export const account = privateKeyToAccount('0x...');
```

:::

Consulta la sección [Id Registry](/es/reference/contracts/reference/id-registry#transfer) para más detalles.
