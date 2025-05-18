# Referencia de la API de Warpcast

Esta página documenta las APIs públicas proporcionadas por Warpcast con información que no está disponible en el protocolo.

El nombre de host siempre es `https://api.warpcast.com`.

## Paginación

Los endpoints paginados devuelven una propiedad `next.cursor` junto al objeto `result`. Para obtener la siguiente página, envía el valor como parámetro de consulta `cursor`. Se puede usar un parámetro opcional `limit` para especificar el tamaño de la página.

```json
{
  "result": {
    ...
  },
  "next": {
    "cursor": "eyJwYWdlIjoxLCJsaW1pdCI6MTAwfQ"
  }
}
```

## Autenticación

Los endpoints autenticados utilizan un token autofirmado, firmado como una App Key para FID:

```tsx
import { NobleEd25519Signer } from "@farcaster/hub-nodejs";

// claves privada/pública de una App Key que posees para un FID
const fid = 6841; //reemplazar
const privateKey = 'secret'; // reemplazar
const publicKey = 'pubkey'; // reemplazar
const signer = new NobleEd25519Signer(new Uint8Array(Buffer.from(privateKey)));

const header = {
  fid,
  type: 'app_key',
  key: publicKey
};
const encodedHeader = Buffer.from(JSON.stringify(header)).toString('base64url');

const payload = { exp: Math.floor(Date.now() / 1000) + 300 }; // 5 minutos
const encodedPayload = Buffer.from(JSON.stringify(payload)).toString('base64url');

const signatureResult = await signer.signMessageHash(Buffer.from(`${encodedHeader}.${encodedPayload}`, 'utf-8'));
if (signatureResult.isErr()) {
  throw new Error("Failed to sign message");
}

const encodedSignature = Buffer.from(signatureResult.value).toString("base64url");

const authToken = encodedHeader + "." + encodedPayload + "." + encodedSignature;

await got.post(
  "https://api.warpcast.com/fc/channel-follows",
  {
	  body: { channelKey: 'evm' }
	  headers: {
	    'Content-Type': 'application/json',
	    'Authorization': 'Bearer ' + authToken;
	  }
	}
)
```

## Conceptos

- Canales: Warpcast tiene el concepto de canales que se basan en FIP-2 (establecer `parentUrl` en los casts). Puedes leer más sobre canales en la [documentación](https://www.notion.so/warpcast/Channels-4f249d22575348a5a0488b5d86f0dd1c?pvs=4).

## Obtener Todos los Canales

`GET /v2/all-channels`

Lista todos los canales. Sin parámetros. No paginado. No autenticado.

Devuelve: un array `channels` con propiedades:

- `id` - id único del canal que no puede cambiarse (llamado 'Name' al crear un canal)
- `url` - `parentUrl` de FIP-2 usado para casts principales en el canal
- `name` - nombre amigable mostrado a los usuarios (llamado 'Display name' al editar un canal)
- `description` - descripción del canal, si está presente
- `descriptionMentions` - un array de los fids de usuario mencionados en la descripción. Múltiples menciones resultan en múltiples entradas.
- `descriptionMentionsPositions` - los índices dentro de la descripción donde aparecen los usuarios mencionados (de `descriptionMentions`). Este array siempre tiene el mismo tamaño que `descriptionMentions`. La mención se coloca a la izquierda de cualquier carácter existente en ese índice.
- `leadFid` - fid del usuario que creó el canal, si está presente
- `moderatorFids` - fids de los moderadores (bajo el nuevo esquema de membresía de canales)
- `createdAt` - tiempo UNIX cuando se creó el canal, en segundos
- `followerCount` - número de usuarios que siguen el canal
- `memberCount` - número de miembros del canal, incluyendo al propietario y moderadores
- `pinnedCastHash` - hash del cast fijado en el canal, si está presente
- `publicCasting` - `true`/`false` indicando si el canal permite que cualquiera publique en él, o solo miembros
- `externalLink` - enlace externo que aparece en el encabezado en Warpcast, si está presente, con 2 propiedades:
  - `title` - título mostrado en el encabezado del canal
  - `url` - url del enlace

```json
{
  "result": {
    "channels": [
      {
        "id": "illustrations",
        "url": "https://warpcast.com/~/channel/illustrations",
        "name": "illustrations",
        "description": "Share your wips, sketches, arts, drops, GMs, artworks you adore or collected — all content related to illustration, tag  to join ⊹ ࣪ ˖ cover by ",
        "descriptionMentions": [
          367850,
          335503
        ],
        "descriptionMentionsPositions": [
          122,
          151
        ],
        "imageUrl": "https://imagedelivery.net/BXluQx4ige9GuW0Ia56BHw/7721c951-b0ed-44ee-aa9c-c31507b69c00/original",
        "headerImageUrl": "https://imagedelivery.net/BXluQx4ige9GuW0Ia56BHw/64efe955-c3ab-4aad-969d-1aed978a3e00/original",
        "leadFid": 367850,
        "moderatorFids": [
          367850
        ],
        "createdAt": 1709753166,
        "followerCount": 2361,
        "memberCount": 300,
        "pinnedCastHash": "0x3ef52987ccacd89af096a753c07efcd55a93e143",
        "publicCasting": false,
        "externalLink": {
          "title": "/creatorssupport",
          "url": "https://warpcast.com/~/channel/creators-support"
        }
      },
      ...
    ]
  }
}
```

Ejemplo:

```bash
curl 'https://api.warpcast.com/v2/all-channels'
```

## Obtener un Canal

`GET /v1/channel`

Obtiene un solo canal. No autenticado.

Parámetros de consulta:

- `channelId` - el id del canal

Devuelve: un objeto de canal único, como se documenta en el endpoint "Obtener Todos los Canales" anterior.

```json
{
  "result": {
    "channel": {
      "id": "illustrations",
      "url": "https://warpcast.com/~/channel/illustrations",
      "name": "illustrations",
      "description": "Share your wips, sketches, arts, drops, GMs, artworks you adore or collected — all content related to illustration, tag  to join ⊹ ࣪ ˖ cover by ",
      "descriptionMentions": [367850, 335503],
      "descriptionMentionsPositions": [122, 151],
      "imageUrl": "https://imagedelivery.net/BXluQx4ige9GuW0Ia56BHw/7721c951-b0ed-44ee-aa9c-c31507b69c00/original",
      "headerImageUrl": "https://imagedelivery.net/BXluQx4ige9GuW0Ia56BHw/64efe955-c3ab-4aad-969d-1aed978a3e00/original",
      "leadFid": 367850,
      "moderatorFids": [367850],
      "createdAt": 1709753166,
      "followerCount": 2361,
      "memberCount": 300,
      "pinnedCastHash": "0x3ef52987ccacd89af096a753c07efcd55a93e143",
      "publicCasting": false,
      "externalLink": {
        "title": "/creatorssupport",
        "url": "https://warpcast.com/~/channel/creators-support"
      }
    }
  }
}
```

```bash
curl 'https://api.warpcast.com/v1/channel?channelId=illustrations'
```

## Obtener Seguidores de un Canal

`GET /v1/channel-followers`

Lista los seguidores de un canal. Ordenados por el momento en que se siguió el canal, descendente. Paginado. No autenticado.

Parámetros de consulta:

- `channelId` - el id del canal

Devuelve: un array `users` con propiedades:

- `fid` - el fid del usuario
- `followedAt` - tiempo UNIX cuando se siguió el canal, en segundos

```json
{
  "result": {
    "users": [
      {
        "fid": 466624,
        "followedAt": 1712685183
      },
      {
        "fid": 469283,
        "followedAt": 1712685067
      },
      ...
    ],
  },
  "next": { "cursor": "..." }
}
```

Ejemplo:

```bash
curl 'https://api.warpcast.com/v1/channel-followers?channelId=books'
```

## Obtener Canales que un Usuario Sigue

`GET /v1/user-following-channels`

Lista todos los canales que un usuario sigue. Ordenados por el momento en que se siguió el canal, descendente. Paginado. No autenticado.

Parámetros:

- `fid` - el fid del usuario

Devuelve: un array `channels` con propiedades:

- Todas las propiedades documentadas en el endpoint "Obtener Todos los Canales" anterior
- `followedAt` - tiempo UNIX cuando se siguió el canal, en segundos

```json
{
  "result": {
    "channels": [
      {
        "id": "fc-updates",
        "url": "https://warpcast.com/~/channel/fc-updates",
        "name": "fc-updates",
        "description": "Important updates about things happening in Farcaster",
        "imageUrl": "https://i.imgur.com/YnnrPaH.png",
        "leadFid": 2,
        "moderatorFid": 5448,
        "moderatorFids": [
          5448,
          3
        ],
        "createdAt": 1712162074,
        "followerCount": 17034,
        "followedAt": 1712162620
      },
      ...
    ]
  },
  "next": { "cursor": "..." }
}
```

Ejemplo:

```bash
curl 'https://api.warpcast.com/v1/user-following-channels?fid=3'
```

## Obtener Estado de Seguimiento de Canal por Usuario

`GET /v1/user-channel`

Verifica si un usuario sigue un canal.

Parámetros de consulta:

- `fid` - el fid del usuario
- `channelId` - el id del canal

Devuelve: 2 propiedades:

- `following` - indica si el canal está siendo seguido
- `followedAt` - tiempo UNIX cuando se siguió el canal, en segundos (opcional, solo presente cuando el canal está siendo seguido)

```json
{
  "result": {
    "following": true,
    "followedAt": 1687943747
  }
}
```

Ejemplo:

```bash
curl 'https://api.warpcast.com/v1/user-channel?fid=3&channelId=books'
```

## Obtener Miembros de un Canal

`GET /fc/channel-members`

Lista los miembros de un canal. Ordenados por el momento en que el usuario se convirtió en miembro, descendente. Paginado. No autenticado.

Parámetros de consulta:

- `channelId` - el id del canal
- `fid` - (opcional) y fid de un usuario para filtrar

Devuelve: un array `members`:

- `fid` - el fid del miembro
- `memberAt` - tiempo UNIX cuando el usuario se convirtió en miembro, en segundos

```json
{
  "result": {
    "members": [
      {
        "fid": 466624,
        "memberAt": 1712685183
      },
      {
        "fid": 469283,
        "memberAt": 1712685067
      },
      ...
    ]
  },
  "next": { "cursor": "..." }
}
```

Ejemplo:

```bash
curl 'https://api.warpcast.com/fc/channel-members?channelId=memes'
```

## Obtener Invitaciones a Canales

`GET /fc/channel-invites`

Lista invitaciones pendientes a canales. Ordenadas por el momento en que se invitó al usuario, descendente. Paginado. No autenticado.

Hay máximo una invitación pendiente por usuario (`invitedFid`) y canal (`channelId`).

Parámetros de consulta:

- `channelId` - (opcional) un id de un canal para filtrar
- `fid` - (opcional) un fid de un usuario para filtrar

Devuelve: un array `invites`:

- `channelId` - el id del canal al que se invitó al usuario
- `invitedFid` - el fid del usuario siendo invitado
- `invitedAt` - tiempo UNIX cuando se invitó al usuario, en segundos
- `inviterFid` - el fid del usuario que creó la invitación
- `role` - el rol al que se invitó al usuario, `member` o `moderator`

```json
{
  "result": {
    "invites": [
      {
        "channelId": "coke-zero",
        "invitedFid": 194,
        "invitedAt": 1726879628,
        "inviterFid": 18949,
        "role": "member"
      },
      {
        "channelId": "brain-teasers",
        "invitedFid": 627785,
        "invitedAt": 1726874566,
        "inviterFid": 235128,
        "role": "member"
      },
      ...
    ]
  },
  "next": { "cursor": "..." }
}
```

Ejemplo:

```bash
curl 'https://api.warpcast.com/fc/channel-invites?channelId=memes'
```

## Obtener Acciones de Moderación de Casts

`GET /fc/moderated-casts`

Lista acciones de moderación. Ordenadas por el momento en que se tomó la acción, descendente. Paginado. No autenticado.

Parámetros de consulta:

- `channelId` - (opcional) un id de un canal para filtrar

Devuelve: un array `moderationActions`:

- `castHash` - hash del cast que fue moderado (incluyendo prefijo `0x`)
- `channelId` - id del canal en el que reside el cast
- `action` - `hide` o `unhide`
- `moderatedAt` - tiempo UNIX cuando ocurrió la moderación, en segundos

```json
{
  "result": {
    "moderationActions": [
      {
        "castHash": "0x6b2253105ef8c1d1b984a5df87182b105a1f0b3a",
        "channelId": "welcome",
        "action": "hide",
        "moderatedAt": 1727767637
      },
      ...
    ]
  },
  "next": { "cursor": "..." }
}
```

Ejemplo:

```bash
curl 'https://api.warpcast.com/fc/moderated-casts?channelId=welcome'
```

## Obtener Usuarios Restringidos de Canal

`GET /fc/channel-restricted-users`

Obtiene usuarios restringidos de unirse a canales mediante un enlace de invitación (aún pueden ser invitados manualmente). Los usuarios entran en este estado automáticamente después de ser eliminados como miembros, y después de ser desbaneados. Los usuarios salen de este estado después de ser invitados (incluso si no han aceptado/rechazado la invitación), y siendo baneados. No es posible restringir o dejar de restringir usuarios directamente. Ordenados por el momento en que el usuario fue restringido, descendente. Paginado. No autenticado.

**Nota:**

- Las respuestas de usuarios restringidos aún son visibles debajo del pliegue
- Este endpoint devuelve solo usuarios activamente restringidos. Si un usuario fue restringido y posteriormente invitado de nuevo o baneado, la entrada de este endpoint desaparecerá.

Parámetros de consulta:

- `channelId` - (opcional) id de canal para filtrar
- `fid` - (opcional) fid de usuario para filtrar

Devuelve: un array `restrictedUsers`:

- `fid` - el fid del usuario restringido
- `channelId` - el id del canal del que el usuario está restringido
- `restrictedAt` - tiempo UNIX cuando comenzó la restricción, en segundos

```json
{
  "result": {
    "restrictedUsers": [
      {
        "fid": 1234,
        "channelId": "welcome",
        "restrictedAt": 1727767637
      },
      ...
    ]
  },
  "next": { "cursor": "..." }
}
```

Ejemplo:

```bash
curl 'https://api.warpcast.com/fc/channel-restricted-users?channelId
```
