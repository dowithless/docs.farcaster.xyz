# Referencia de la API del Servidor de Registro FName

El [servidor de registro Fname](https://github.com/farcasterxyz/fname-registry) está alojado en https://fnames.farcaster.xyz

Es un servicio HTTP simple responsable de emitir y rastrear fnames. Todos los cambios de Fname se registran como una transferencia.  
Registrar un fname es una transferencia desde el FID 0 al fid del usuario. Transferir un fname es una transferencia desde el fid del usuario a otro fid. Cancelar el registro de un fname es una transferencia desde el fid del usuario al fid 0.

::: warning Registrar un fname

Nota: al registrar un nuevo fname, llamar a esta API no es suficiente. Esto solo reserva el nombre para tu fid. También debes enviar un mensaje [UserDataAdd](/es/reference/hubble/datatypes/messages#_2-userdata) al hub para establecer este nombre como tu nombre de usuario.

:::

### Obtener historial de transferencias

Para obtener un historial de todas las transferencias, realiza una solicitud GET a `/transfers`

```bash
curl https://fnames.farcaster.xyz/transfers | jq
```

También acepta los siguientes parámetros de consulta:

- `from_id` - El ID de transferencia desde el que comenzar la paginación
- `name` - El fname por el que filtrar
- `fid` - El fid (ya sea from o to) por el que filtrar
- `from_ts` - El timestamp (en segundos) desde el que comenzar la paginación

### Obtener fname o fid actual

Para obtener el evento de transferencia más reciente para un fid o fname, realiza una solicitud GET a `/transfers/current`

Ejemplo: Para determinar el fid de `@farcaster`, realiza la siguiente llamada y usa el valor del campo `to` en la respuesta

```bash
curl https://fnames.farcaster.xyz/transfers?name=farcaster | jq
```

Para determinar el fname del fid `1`, realiza la siguiente llamada y usa el valor del campo `username` en la respuesta

```bash
curl https://fnames.farcaster.xyz/transfers?fid=1 | jq
```

Ambos devolverán el mismo objeto de transferencias:

```json
{
  "transfers": [
    {
      "id": 1,
      "timestamp": 1628882891,
      "username": "farcaster",
      "owner": "0x8773442740c17c9d0f0b87022c722f9a136206ed",
      "from": 0,
      "to": 1,
      "user_signature": "0xa6fdd2a69deab5633636f32a30a54b21b27dff123e6481532746eadca18cd84048488a98ca4aaf90f4d29b7e181c4540b360ba0721b928e50ffcd495734ef8471b",
      "server_signature": "0xb7181760f14eda0028e0b647ff15f45235526ced3b4ae07fcce06141b73d32960d3253776e62f761363fb8137087192047763f4af838950a96f3885f3c2289c41b"
    }
  ]
}
```

### Registrar o transferir un fname

Para registrar un nuevo fid, por ejemplo `hubble`, primero asegúrate de que el fname no esté ya registrado.

Luego realiza una solicitud POST a `/transfers` con el siguiente cuerpo:

```yaml
{
  "name": "hubble", // Nombre a registrar
  "from": 0,  // Fid desde el que se transfiere (0 para un nuevo registro)
  "to": 123, // Fid al que se transfiere (0 para cancelar el registro)
  "fid": 123, // Fid que realiza la solicitud (debe coincidir con from o to)
  "owner": "0x...", // Dirección de custodia del fid que realiza la solicitud
  "timestamp": 1641234567,  // Timestamp actual en segundos
  "signature": "0x..."  // Firma EIP-712 firmada por la dirección de custodia del fid
}
```

Para generar la firma EIP-712, usa el siguiente código:

```js
import { makeUserNameProofClaim, EIP712Signer } from '@farcaster/hub-nodejs';

const accountKey: EIP712Signer = undefined; // Clave de cuenta para la dirección de custodia (usa la subclase apropiada de hub-nodejs para ethers o viem)

const claim = makeUserNameProofClaim({
  name: 'hubble',
  owner: '0x...',
  timestamp: Math.floor(Date.now() / 1000),
});
const signature = (
  await accountKey.signUserNameProofClaim(claim)
)._unsafeUnwrap();
```

Este es el mismo tipo de firma utilizado en los UsernameProofs de ENS proporcionados a los hubs para demostrar la propiedad de un nombre ENS.

Ejemplo:

```bash
curl -X POST https://fnames.farcaster.xyz/transfers \
  -H "Content-Type: application/json" \
  -d \
'{"name": "hubble", "owner": "0x...", "signature": "0x...", "from": 0, "to": 1000, "timestamp": 1641234567, fid: 1000}'
```

Una vez que un nombre está registrado, aún se necesita enviar un mensaje [UserData](/es/reference/hubble/datatypes/messages#_2-userdata) al hub para establecer realmente el nombre de usuario. Consulta ejemplos en el repositorio [hub-nodejs](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/hub-nodejs/examples/hello-world).
