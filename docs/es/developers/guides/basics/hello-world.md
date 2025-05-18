# Hola Mundo

Crea tu cuenta de Farcaster programáticamente y publica tu primer mensaje "Hola Mundo".

Este ejemplo te muestra cómo:

- Realizar transacciones onchain para crear una cuenta
- Alquilar una unidad de almacenamiento para poder publicar mensajes
- Añadir una clave de cuenta para firmar mensajes
- Adquirir un fname para tu cuenta
- Crear, firmar y publicar mensajes

Este ejemplo está disponible como un repositorio completamente funcional [aquí](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/hub-nodejs/examples/hello-world).

### Requisitos

- Acceso de escritura a un hub (ya sea propio o de un tercero)
- Una wallet ETH con aproximadamente ~10$ USD de ETH puenteados a [Optimism](https://www.optimism.io/)
- Una URL RPC de ETH para OP Mainnet (por ejemplo, a través de [Alchemy](https://www.alchemy.com/), [Infura](https://www.infura.io/) o [QuickNode](https://www.quicknode.com/)).

## 1. Configurar constantes

```typescript
import {
  ID_GATEWAY_ADDRESS,
  idGatewayABI,
  KEY_GATEWAY_ADDRESS,
  keyGatewayABI,
  ID_REGISTRY_ADDRESS,
  idRegistryABI,
  FarcasterNetwork,
} from '@farcaster/hub-web';
import { zeroAddress } from 'viem';
import { optimism } from 'viem/chains';

/**
 * Rellena las siguientes constantes con tus propios valores
 */
const MNEMONIC = '<REQUIRED>';
const OP_PROVIDER_URL = '<REQUIRED>'; // URL de Alchemy o Infura
const RECOVERY_ADDRESS = zeroAddress; // Opcional, usar el valor por defecto significa que la cuenta no podrá recuperarse más tarde si se pierde el mnemónico
const ACCOUNT_KEY_PRIVATE_KEY: Hex = zeroAddress; // Opcional, usar el valor por defecto significa que se creará una nueva clave de cuenta cada vez

// Nota: hoyt es el hub mainnet del equipo de Farcaster, que está protegido por contraseña para evitar abusos. Usa un proveedor de hub de terceros
// como https://neynar.com/ O, ejecuta tu propio hub mainnet y transmite a él sin permisos.
const HUB_URL = 'hoyt.farcaster.xyz:2283'; // URL + Puerto del Hub
const HUB_USERNAME = ''; // Nombre de usuario para autenticación, déjalo en blanco si no usas TLS
const HUB_PASS = ''; // Contraseña para autenticación, déjalo en blanco si no usas TLS
const USE_SSL = false; // configúralo como true si te comunicas con un hub que usa SSL (hubs alojados por terceros o hubs que requieren autenticación)
const FC_NETWORK = FarcasterNetwork.MAINNET; // Red del Hub

const CHAIN = optimism;

const IdGateway = {
  abi: idGatewayABI,
  address: ID_GATEWAY_ADDRESS,
  chain: CHAIN,
};
const IdContract = {
  abi: idRegistryABI,
  address: ID_REGISTRY_ADDRESS,
  chain: CHAIN,
};
const KeyContract = {
  abi: keyGatewayABI,
  address: KEY_GATEWAY_ADDRESS,
  chain: CHAIN,
};
```

## 2. Registrar y pagar por almacenamiento

Crea una función para registrar un FID y pagar por almacenamiento. Esta función verificará si la cuenta ya tiene un FID
y terminará temprano si es así.

```typescript
const getOrRegisterFid = async (): Promise<number> => {
  const balance = await walletClient.getBalance({ address: account.address });
  // Verificar si ya tenemos un fid
  const existingFid = (await walletClient.readContract({
    ...IdContract,
    functionName: 'idOf',
    args: [account.address],
  })) as bigint;

  if (existingFid > 0n) {
    return parseInt(existingFid.toString());
  }

  const price = await walletClient.readContract({
    ...IdGateway,
    functionName: 'price',
  });
  if (balance < price) {
    throw new Error(
      `Saldo insuficiente para alquilar almacenamiento, requerido: ${price}, saldo: ${balance}`
    );
  }
  const { request: registerRequest } = await walletClient.simulateContract({
    ...IdGateway,
    functionName: 'register',
    args: [RECOVERY_ADDRESS],
    value: price,
  });
  const registerTxHash = await walletClient.writeContract(registerRequest);
  const registerTxReceipt = await walletClient.waitForTransactionReceipt({
    hash: registerTxHash,
  });
  // Ahora extrae el FID de los logs
  const registerLog = decodeEventLog({
    abi: idRegistryABI,
    data: registerTxReceipt.logs[0].data,
    topics: registerTxReceipt.logs[0].topics,
  });
  const fid = parseInt(registerLog.args['id']);
  return fid;
};

const fid = await getOrRegisterFid();
```

## 3. Añadir una clave de cuenta

Ahora, añadiremos una clave de cuenta al registro de claves. Cada clave de cuenta debe tener un campo de metadatos firmado por el fid de la app que lo solicita.
En nuestro caso, usaremos nuestro propio fid. Nota, esto requiere que firmes un mensaje con la clave privada de la dirección
que posee el fid. Si esto no es posible, registra primero un fid separado para la app y usa ese.

```typescript
const getOrRegisterAccountKey = async (fid: number) => {
  if (ACCOUNT_KEY_PRIVATE_KEY !== zeroAddress) {
    // Si se proporciona una clave privada, asumimos que la clave de cuenta ya está en el registro de claves
    const privateKeyBytes = fromHex(ACCOUNT_KEY_PRIVATE_KEY, 'bytes');
    const publicKeyBytes = ed25519.getPublicKey(privateKeyBytes);
    return privateKeyBytes;
  }

  const privateKey = ed25519.utils.randomPrivateKey();
  const publicKey = toHex(ed25519.getPublicKey(privateKey));

  const eip712signer = new ViemLocalEip712Signer(appAccount);
  // Para añadir una clave, necesitamos firmar los metadatos con el fid de la app en cuyo nombre estamos añadiendo la clave
  // Usa tu fid personal, o registra un fid separado para la app
  const metadata = await eip712signer.getSignedKeyRequestMetadata({
    requestFid: APP_FID,
    key: APP_PRIVATE_KEY,
    deadline: Math.floor(Date.now() / 1000) + 60 * 60, // 1 hora a partir de ahora
  });

  const { request: signerAddRequest } = await walletClient.simulateContract({
    ...KeyContract,
    functionName: 'add',
    args: [1, publicKey, 1, metadata], // keyType, publicKey, metadataType, metadata
  });

  const accountKeyAddTxHash = await walletClient.writeContract(
    signerAddRequest
  );
  await walletClient.waitForTransactionReceipt({ hash: accountKeyAddTxHash });
  // Esperando 30 segundos para permitir que los hubs recojan la transacción accountKey
  await new Promise((resolve) => setTimeout(resolve, 30000));
  return privateKey;
};

const accountPrivateKey = await getOrRegisterAccountKey(fid);
```

## 4. Registrar un fname

Ahora que las acciones onchain están completas, registremos un fname usando el registro offchain de fnames de farcaster.
Registrar un fname requiere una firma de la dirección de custodia del fid.

```typescript
const registerFname = async (fid: number) => {
  try {
    // Primero verifica si este fid ya tiene un fname
    const response = await axios.get(
      `https://fnames.farcaster.xyz/transfers/current?fid=${fid}`
    );
    const fname = response.data.transfer.username;
    return fname;
  } catch (e) {
    // Sin nombre de usuario, ignorar y continuar con el registro
  }

  const fname = `fid-${fid}`;
  const timestamp = Math.floor(Date.now() / 1000);
  const userNameProofSignature = await walletClient.signTypedData({
    domain: USERNAME_PROOF_DOMAIN,
    types: USERNAME_PROOF_TYPE,
    primaryType: 'UserNameProof',
    message: {
      name: fname,
      timestamp: BigInt(timestamp),
      owner: account.address,
    },
  });

  const response = await axios.post('https://fnames.farcaster.xyz/transfers', {
    name: fname, // Nombre a registrar
    from: 0, // Fid desde el que transferir (0 para un nuevo registro)
    to: fid, // Fid al que transferir (0 para desregistrar)
    fid: fid, // Fid que realiza la solicitud (debe coincidir con from o to)
    owner: account.address, // Dirección de custodia del fid que realiza la solicitud
    timestamp: timestamp, // Marca de tiempo actual en segundos
    signature: userNameProofSignature, // Firma EIP-712 firmada por la dirección de custodia actual del fid
  });
  return fname;
};

const fname = await registerFname(fid);
```

Nota que esto solo asocia el nombre a nuestro fid, todavía necesitamos establecerlo como nuestro nombre de usuario.

## 5. Escribir en el hub

Finalmente, ahora estamos listos para enviar mensajes al hub. Primero, estableceremos el fname como nuestro nombre de usuario. Y luego publicaremos un
cast.

```typescript
const submitMessage = async (resultPromise: HubAsyncResult<Message>) => {
  const result = await resultPromise;
  if (result.isErr()) {
    throw new Error(`Error al crear mensaje: ${result.error}`);
  }
  await hubClient.submitMessage(result.value);
};

const accountKey = new NobleEd25519Signer(accountPrivateKey);
const dataOptions = {
  fid: fid,
  network: FC_NETWORK,
};
const userDataUsernameBody = {
  type: UserDataType.USERNAME,
  value: fname,
};
// Establecer el nombre de usuario
await submitMessage(
  makeUserDataAdd(userDataUsernameBody, dataOptions, accountKey)
);

// Publicar un cast
await submitMessage(
  makeCastAdd(
    {
      text: '¡Hola Mundo!',
    },
    dataOptions,
    accountKey
  )
);
```

Ahora, puedes ver tu perfil en cualquier cliente de farcaster. Para verlo en Warpcast, visita `https://warpcast.com/@<fname>`
