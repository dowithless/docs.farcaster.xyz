# Cambiar nombre en Farcaster

Un usuario puede cambiar su nombre ENS offchain o Fname sin afectar el historial de su cuenta. Esto se puede hacer como máximo una vez cada 28 días.

::: warning

- Los Fnames pueden ser revocados si violas la [política de uso](/es/learn/architecture/ens-names#offchain-ens-names-fnames).
- Las aplicaciones pueden reducir tu reputación si cambias de Fname con frecuencia.
  :::

### Requisitos

- Una wallet ETH que posea la cuenta en OP Mainnet. No se requiere ETH.

### Cambiar nombre de usuario

Para transferir un Fname, por ejemplo `Hubble`, realiza una solicitud POST a `/transfers` con el siguiente cuerpo:

```yaml
{
  "name": "hubble", // Nombre a transferir
  "from": 123,  // FID desde el que se transfiere
  "to": 321, // FID al que se transfiere
  "fid": 123, // FID que realiza la solicitud (debe coincidir con from)
  "owner": "0x...", // Dirección de custodia del FID que realiza la solicitud
  "timestamp": 1641234567,  // Marca de tiempo actual en segundos
  "signature": "0x..."  // Firma EIP-712 firmada por la dirección de custodia del FID
}
```

Para generar la firma EIP-712, utiliza el siguiente código:

```js
import { makeUserNameProofClaim, EIP712Signer } from '@farcaster/hub-nodejs';

const accountKey: EIP712Signer = undefined; // Account Key para la dirección de custodia (usa la subclase apropiada de hub-nodejs para ethers o viem)

const claim = makeUserNameProofClaim({
  name: 'hubble',
  owner: '0x...',
  timestamp: Math.floor(Date.now() / 1000),
});
const signature = (
  await accountKey.signUserNameProofClaim(claim)
)._unsafeUnwrap();
```

Ejemplo de solicitud mediante curl:

```bash
curl -X POST https://fnames.farcaster.xyz/transfers \
  -H "Content-Type: application/json" \
  -d \
'{"name": "hubble", "owner": "0x...", "signature": "0x...", "from": 123, "to": 321, "timestamp": 1641234567, fid: 123}'
```

Consulta [aquí](/es/reference/fname/api.md) para más detalles sobre la API del registro de Fnames.
