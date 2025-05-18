# Solicitudes de Firmante (Signer Requests)

Si tu aplicación desea escribir datos en Farcaster en nombre de un usuario, la aplicación debe obtener que el usuario agregue una clave de firma para su aplicación.

## Guía

### Requisitos previos

- Un FID registrado

### 1. Un usuario autenticado hace clic en "Conectar con Warpcast" en tu aplicación

Una vez que tu aplicación pueda identificar y autenticar a un usuario, puedes presentarle la opción de conectarse con Warpcast.

### 2. Genera un nuevo par de claves Ed25519 para el usuario y una firma SignedKeyRequest

Tu aplicación debe generar y almacenar de forma segura un par de claves Ed25519 asociado a este usuario. En los siguientes pasos, solicitarás al usuario que apruebe este par de claves para firmar mensajes de Farcaster en su nombre.

Es importante que:

- La clave privada se almacene de forma segura y nunca se exponga
- El par de claves pueda recuperarse y usarse para firmar mensajes cuando el usuario regrese

Además de generar un nuevo par de claves, tu aplicación debe producir una firma ECDSA utilizando la dirección de custodia de su FID. Esto permite que la clave se atribuya a la aplicación, lo cual es útil para una amplia gama de cosas, desde saber qué aplicaciones se están utilizando hasta filtrar contenido basado en las aplicaciones que los generaron.

**Código de ejemplo:**

```ts
import * as ed from '@noble/ed25519';
import { mnemonicToAccount, signTypedData } from 'viem/accounts';

/*** Código auxiliar EIP-712 ***/

const SIGNED_KEY_REQUEST_VALIDATOR_EIP_712_DOMAIN = {
  name: 'Farcaster SignedKeyRequestValidator',
  version: '1',
  chainId: 10,
  verifyingContract: '0x00000000fc700472606ed4fa22623acf62c60553',
} as const;

const SIGNED_KEY_REQUEST_TYPE = [
  { name: 'requestFid', type: 'uint256' },
  { name: 'key', type: 'bytes' },
  { name: 'deadline', type: 'uint256' },
] as const;

/*** Generando un par de claves ***/

const privateKey = ed.utils.randomPrivateKey();
const publicKeyBytes = await ed.getPublicKey(privateKey);
const key = '0x' + Buffer.from(publicKeyBytes).toString('hex');

/*** Generando una firma Signed Key Request ***/

const appFid = process.env.APP_FID;
const account = mnemonicToAccount(process.env.APP_MNENOMIC);

const deadline = Math.floor(Date.now() / 1000) + 86400; // la firma es válida por 1 día
const signature = await account.signTypedData({
  domain: SIGNED_KEY_REQUEST_VALIDATOR_EIP_712_DOMAIN,
  types: {
    SignedKeyRequest: SIGNED_KEY_REQUEST_TYPE,
  },
  primaryType: 'SignedKeyRequest',
  message: {
    requestFid: BigInt(appFid),
    key,
    deadline: BigInt(deadline),
  },
});
```

**Fecha límite (Deadline)**

Este valor controla cuánto tiempo es válida la firma Signed Key Request. Es una marca de tiempo Unix en segundos (nota: JavaScript usa milisegundos). Recomendamos establecerlo en 24 horas.

### 3. La aplicación usa la clave pública + firma SignedKeyRequest para iniciar una Solicitud de Clave Firmada usando la API de Warpcast

La aplicación llama al backend de Warpcast, que devuelve un enlace profundo (deeplink) y un token de sesión que puede usarse para verificar el estado de la solicitud.

```ts
/*** Creando una Solicitud de Clave Firmada ***/

const warpcastApi = 'https://api.warpcast.com';
const { token, deeplinkUrl } = await axios
  .post(`${warpcastApi}/v2/signed-key-requests`, {
    key: publicKey,
    requestFid: fid,
    signature,
    deadline,
  })
  .then((response) => response.data.result.signedKeyRequest);

// deeplinkUrl debe presentarse al usuario
// token debe usarse para hacer polling
```

**URL de redirección (Redirect URL)**

Si esta solicitud se realiza desde una aplicación móvil nativa que puede abrir enlaces profundos, puedes incluir un redirectUrl al que el usuario debería ser redirigido después de completar la solicitud.

Nota: si tu aplicación es una PWA o aplicación web, no incluyas este valor, ya que el usuario será llevado a una sesión sin estado.

**Patrocinios (Sponsorships)**

Puedes patrocinar las tarifas onchain para el usuario. Consulta [Patrocinar un firmante](#sponsoring-a-signer) más abajo.

### 4. La aplicación presenta el enlace profundo de la respuesta al usuario

La aplicación presenta el enlace profundo, lo que pedirá al usuario que abra la aplicación Warpcast y autorice la solicitud del firmante (capturas de pantalla al final). La aplicación debe indicar al usuario que abra el enlace en su dispositivo móvil donde tenga instalado Warpcast:

1. En móvil, activar el enlace profundo directamente
2. En web, mostrar el enlace profundo como un código QR para escanear

**Código de ejemplo**

```ts
import QRCode from 'react-qr-code';

const DeepLinkQRCode = (deepLinkUrl) => <QRCode value={deepLinkUrl} />;
```

### 5. La aplicación comienza a sondear el endpoint de Solicitud de Firmante usando el token

Una vez que se ha presentado el enlace profundo al usuario, la aplicación debe esperar a que el usuario complete el flujo de solicitud del firmante. La aplicación puede sondear el recurso de solicitud de firmante y buscar los datos que indiquen que el usuario ha completado la solicitud:

````ts
const poll = async (token: string) => {
  while (true) {
    // esperar 1s
    await new Promise((r) => setTimeout(r, 2000));

    console.log('sondeando solicitud de clave firmada');
    const signedKeyRequest = await axios
      .get(`${warpcastApi}/v2/signed-key-request`, {
        params: {
          token,
        },
      })
      .then((response) => response.data.result.signedKeyRequest);

    if (signedKeyRequest.state === 'completed') {
      console.log('Solicitud de Clave Firmada completada:', signedKeyRequest);

      /**
       * En este punto, el firmante ha sido registrado onchain y puedes comenzar a enviar
       * mensajes a hubs firmados por su clave:
       * ```
       * const signer = Ed25519Signer.fromPrivateKey(privateKey)._unsafeUnwrap();
       * const message = makeCastAdd(..., signer)
       * ```
       */
      break;
    }
  }
};

poll(token);
````

### 6. El usuario abre el enlace y completa el flujo de Solicitud de Firmante en Warpcast

Cuando el usuario aprueba la solicitud en Warpcast, se realizará una transacción onchain que otorga permisos de escritura a ese firmante. Una vez que se complete, tu aplicación debería indicar éxito y puede comenzar a escribir mensajes usando la clave recién agregada.

### Implementación de referencia

````ts
import * as ed from '@noble/ed25519';
import { Hex } from 'viem';
import { mnemonicToAccount } from 'viem/accounts';
import axios from 'axios';
import * as qrcode from 'qrcode-terminal';

/*** Código auxiliar EIP-712 ***/

const SIGNED_KEY_REQUEST_VALIDATOR_EIP_712_DOMAIN = {
  name: 'Farcaster SignedKeyRequestValidator',
  version: '1',
  chainId: 10,
  verifyingContract: '0x00000000fc700472606ed4fa22623acf62c60553',
} as const;

const SIGNED_KEY_REQUEST_TYPE = [
  { name: 'requestFid', type: 'uint256' },
  { name: 'key', type: 'bytes' },
  { name: 'deadline', type: 'uint256' },
] as const;

(async () => {
  /*** Generando un par de claves ***/

  const privateKey = ed.utils.randomPrivateKey();
  const publicKeyBytes = await ed.getPublicKey(privateKey);
  const key = '0x' + Buffer.from(publicKeyBytes).toString('hex');

  /*** Generando una firma Signed Key Request ***/

  const appFid = process.env.APP_FID;
  const account = mnemonicToAccount(process.env.APP_MNEMONIC);

  const deadline = Math.floor(Date.now() / 1000) + 86400; // la firma es válida por 1 día
  const signature = await account.signTypedData({
    domain: SIGNED_KEY_REQUEST_VALIDATOR_EIP_712_DOMAIN,
    types: {
      SignedKeyRequest: SIGNED_KEY_REQUEST_TYPE,
    },
    primaryType: 'SignedKeyRequest',
    message: {
      requestFid: BigInt(appFid),
      key,
      deadline: BigInt(deadline),
    },
  });

  /*** Creando una Solicitud de Clave Firmada ***/

  const warpcastApi = 'https://api.warpcast.com';
  const { token, deeplinkUrl } = await axios
    .post(`${warpcastApi}/v2/signed-key-requests`, {
      key,
      requestFid: appFid,
      signature,
      deadline,
    })
    .then((response) => response.data.result.signedKeyRequest);

  qrcode.generate(deeplinkUrl, console.log);
  console.log('escanea esto con tu teléfono');
  console.log('enlace profundo:', deeplinkUrl);

  const poll = async (token: string) => {
    while (true) {
      // esperar 1s
      await new Promise((r) => setTimeout(r, 2000));

      console.log('sondeando solicitud de clave firmada');
      const signedKeyRequest = await axios
        .get(`${warpcastApi}/v2/signed-key-request`, {
          params: {
            token,
          },
        })
        .then((response) => response.data.result.signedKeyRequest);

      if (signedKeyRequest.state === 'completed') {
        console.log('Solicitud de Clave Firmada completada:', signedKeyRequest);

        /**
         * En este punto, el firmante ha sido registrado onchain y puedes comenzar a enviar
         * mensajes a hubs firmados por su clave:
         * ```
         * const signer = Ed25519Signer.fromPrivateKey(privateKey)._unsafeUnwrap();
         * const message = makeCastAdd(..., signer)
         * ```
         */
        break;
      }
    }
  };

  await poll(token);
})();
````

## API

### POST /v2/signed-key-request

Crea solicitudes de clave firmada.

**Cuerpo (Body):**

- `key` - cadena hexadecimal de la clave pública Ed25519
- `requestFid` - fid de la aplicación solicitante
- `deadline` - marca de tiempo Unix hasta la cual la firma es válida
- `signature` - firma [SignedKeyRequest](https://docs.farcaster.xyz/reference/contracts/reference/signed-key-request-validator#signed-key-request-validator) de la aplicación solicitante
- `redirectUrl` - Opcional. URL a la que redirigir después de aprobar el firmante. Nota: esto solo debe usarse al solicitar un firmante desde una aplicación móvil nativa.
- `sponsorship` -

**Respuesta de ejemplo:**

```json
{
  "result": {
    "signedKeyRequest": {
      "token": "0xa241e6b1287a07f4d3f9c5bd",
      "deeplinkUrl": "farcaster://signed-key-request?token=0xa241e6b1287a07f4d3f9c5bd",
      "key": "0x48b0c7a6deff69bad7673357df43274f3a08163a6440b7a7e3b3cb6b6623faa7",
      "state": "pending"
    }
  }
}
```

### GET /v2/signed-key-request

Obtiene el estado de una solicitud de clave firmada.

**Parámetros de consulta (Query parameters):**

- `token` - token que identifica la solicitud

**Respuesta:**

- `token` - token que identifica la solicitud
- `deeplinkUrl` - URL donde el usuario puede completar la solicitud
- `key` - clave solicitada para agregar
- `state` - estado de la solicitud: `pending` - ninguna acción tomada por el usuario, `approved` - el usuario ha aprobado pero la transacción onchain no está confirmada, `completed` - la transacción onchain está confirmada

**Respuesta de ejemplo en estado pendiente:**

```json
{
  "result": {
    "signedKeyRequest": {
      "token": "0xa241e6b1287a07f4d3f9c5bd",
      "deeplinkUrl": "farcaster://signed-key-request?token=0xa241e6b1287a07f4d3f9c5bd",
      "key": "0x48b0c7a6deff69bad7673357df43274f3a08163a6440b7a7e3b3cb6b6623faa7",
      "state": "pending"
    }
  }
}
```

**Respuesta de ejemplo después de la aprobación pero antes de que se confirme la transacción:**

```json
{
  "result": {
    "signedKeyRequest": {
      "token": "0xa241e6b1287a07f4d3f9c5bd",
      "deeplinkUrl": "farcaster://signed-key-request?token=0xa241e6b1287a07f4d3f9c5bd",
      "key": "0x48b0c7a6deff69bad7673357df43274f3a08163a6440b7a7e3b3cb6b6623faa7",
      "state": "approved",
      "userFid": 1
    }
  }
}
```

**Respuesta de ejemplo después de que se confirme la transacción:**

```json
{
  "result": {
    "signedKeyRequest": {
      "token": "0xa241e6b1287a07f4d3f9c5bd",
      "deeplinkUrl": "farcaster://signed-key-request?token=0xa241e6b1287a07f4d3f9c5bd",
      "key": "0x48b0c7a6deff69bad7673357df43274f3a08163a6440b7a7e3b3cb6b6623faa7",
      "state": "completed",
      "userFid": 1
    }
  }
}
```

## Patrocinar un firmante (Sponsoring a signer)

Una aplicación puede patrocinar un firmante para que el usuario no tenga que pagar. La aplicación debe estar registrada en Warpcast y tener warps ≥ 100.

Al generar una solicitud de clave firmada, una aplicación puede indicar que debe ser patrocinada incluyendo un campo adicional `sponsorship` en el cuerpo de la solicitud.

```ts
type SignedKeyRequestSponsorship = {
  sponsorFid: number;
  signature: string; // firma de patrocinio por sponsorFid
};

type SignedKeyRequestBody = {
  key: string;
  requestFid: number;
  deadline: number;
  signature: string; // firma de solicitud de clave por requestFid
  sponsorship?: SignedKeyRequestSponsorship;
};
```

Para crear un `SignedKeyRequestSponsorship`:

1. Crea el par de claves y haz que el FID solicitante genere una firma
2. Crea una segunda firma del FID patrocinador usando la firma generada en el paso 1 como entrada sin procesar para un mensaje EIP-191

```ts
// sponsoringAccount es una instancia de cuenta Viem para la dirección de custodia del FID patrocinador
// signedKeyRequestSignature es la firma EIP-712 firmada por el FID solicitante
const sponsorSignature = sponsoringAccount.signMessage({
  message: { raw: signedKeyRequestSignature },
});
```

Cuando el usuario abre la solicitud de clave firmada en Warpcast, verá que las tarifas onchain han sido patrocinadas por tu aplicación.
