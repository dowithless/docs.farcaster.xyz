# Decodificar metadatos de clave

Cuando los usuarios añaden una nueva clave al Registro de Claves, el contrato emite [metadatos codificados](/es/reference/contracts/reference/signed-key-request-validator#signedkeyrequestmetadata-struct) en un evento. Puedes usar estos metadatos para determinar quién solicitó la clave.

Para decodificar los metadatos de la clave, puedes usar la función `decodeAbiParameters` de Viem:

::: code-group

```ts [Viem]
import { decodeAbiParameters } from 'viem';

const encodedMetadata =
  '0x' +
  '0000000000000000000000000000000000000000000000000000000000000020' +
  '00000000000000000000000000000000000000000000000000000000000023C0' +
  '00000000000000000000000002EF790DD7993A35FD847C053EDDAE940D055596' +
  '0000000000000000000000000000000000000000000000000000000000000080' +
  '00000000000000000000000000000000000000000000000000000000657C8AF5' +
  '0000000000000000000000000000000000000000000000000000000000000041' +
  'F18569F4364BB2455DB27A4E8464A83AD8555416B1AAB21FF26E8501168F471F' +
  '7EC17BB096EA4438E5BF82CE01224B2F67EF9042737B7B101C41A1A05CB469DC' +
  '1B00000000000000000000000000000000000000000000000000000000000000';

const decoded = decodeAbiParameters(
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
  encodedMetadata
);

console.log(decoded);
/*
[
  {
    requestFid: 9152n,
    requestSigner: '0x02ef790Dd7993A35fD847C053EDdAE940D055596',
    signature: '0xF185...69F4',
    deadline: 1702660853n
  }
]
*/
```

:::

Consulta la referencia del [Validador de Solicitud de Clave Firmada](/es/reference/contracts/reference/signed-key-request-validator#signedkeyrequestmetadata-struct) para más detalles.
