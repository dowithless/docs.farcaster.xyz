# Crear mensajes

Un mensaje representa una acción realizada por un usuario (ej. alice dice "hola mundo")

Existen muchos tipos de mensajes, y este tutorial te guiará a través de los más comunes. Otros tutoriales cubrirán los tipos de mensajes más avanzados.

## Configuración

Necesitarás:

- Permisos de escritura en una instancia de hubble
- Clave privada de un firmante registrado a un fid
- `hub-nodejs` y funciones auxiliares importadas como se muestra a continuación

```ts
import {
  makeCastAdd,
  makeCastRemove,
  makeLinkAdd,
  makeLinkRemove,
  makeReactionAdd,
  makeReactionRemove,
  makeUserDataAdd,
  NobleEd25519Signer,
  FarcasterNetwork,
} from '@farcaster/hub-nodejs';

const ACCOUNT_PRIVATE_KEY: Hex = '0x...'; // Tu clave privada de cuenta
const FID = -1; // Tu fid
const ed25519Signer = new NobleEd25519Signer(ACCOUNT_PRIVATE_KEY);
const dataOptions = {
  fid: FID,
  network: FC_NETWORK,
};
const FC_NETWORK = FarcasterNetwork.MAINNET;
```

## Casts

Los casts son mensajes públicos creados por un usuario.

Un cast se crea emitiendo un mensaje CastAdd con el texto del cast y opcionalmente embeds, menciones y emojis. El ejemplo siguiente muestra la creación de un cast simple.

```typescript
const cast = await makeCastAdd(
  {
    text: '¡Este es un cast!', // El texto puede tener hasta 320 bytes de longitud
    embeds: [],
    embedsDeprecated: [],
    mentions: [],
    mentionsPositions: [],
  },
  dataOptions,
  ed25519Signer
);
```

Un cast puede eliminarse emitiendo un mensaje CastRemove con el hash del mensaje CastAdd y una marca de tiempo posterior.

```typescript
const castRemove = await makeCastRemove(
  {
    targetHash: castReplyingToAUrl._unsafeUnwrap().hash,
  },
  dataOptions,
  ed25519Signer
);
```

Para crear casts con embeds, menciones, canales o emojis, consulta el tutorial de [casts](../writing/casts.md).

## Reacciones

Las reacciones son relaciones fuertemente tipadas entre un usuario y un cast (ej. un "me gusta").

Un usuario "da me gusta" a un cast produciendo un mensaje ReactionAdd con el tipo establecido a `like` y el objetivo establecido al hash del cast y el fid de su autor.

```typescript
const reactionAdd = await makeReactionAdd(
  {
    type: ReactionType.LIKE,
    targetCastId: { fid: createdCast.data.fid, hash: createdCast.hash },
  },
  dataOptions,
  ed25519Signer
);
```

El "me gusta" puede eliminarse transmitiendo un mensaje ReactionRemove con la información y una marca de tiempo posterior.

```typescript
const reactionRemove = await makeReactionRemove(
  {
    type: ReactionType.LIKE,
    targetCastId: { fid: createdCast.data.fid, hash: createdCast.hash },
  },
  dataOptions, // La marca de tiempo proporcionada debe ser mayor
  ed25519Signer
);
```

Un usuario puede "recastear" con un proceso muy similar.

```typescript
const recastAdd = await makeReactionAdd(
  {
    type: ReactionType.RECAST,
    targetCastId: { fid: createdCast.data.fid, hash: createdCast.hash },
  },
  dataOptions,
  ed25519Signer
);

const recastRemove = await makeReactionRemove(
  {
    type: ReactionType.RECAST,
    targetCastId: { fid: createdCast.data.fid, hash: createdCast.hash },
  },
  dataOptions,
  ed25519Signer
);
```

## Datos de Usuario

UserData es un conjunto de mensajes fuertemente tipados que representan metadatos sobre un usuario (ej. biografía, foto de perfil).

Un mensaje `UserData` tiene un tipo y un valor de cadena que puede establecerse. El ejemplo siguiente muestra a un usuario actualizando su biografía.

```typescript
// Actualizar biografía de usuario. Otros campos son similares, solo cambia el tipo. El valor siempre es una cadena.
const bioUpdate = await makeUserDataAdd(
  {
    type: UserDataType.BIO,
    value: 'nueva biografía',
  },
  dataOptions,
  ed25519Signer
);
```

## Enlaces

Los enlaces son relaciones débilmente tipadas entre usuarios (ej. alice sigue a bob).

Un usuario crea un Enlace emitiendo un mensaje LinkAdd con un tipo de cadena y el fid del usuario objetivo. El enlace más comúnmente soportado en todos los clientes es 'follow'.

```typescript
const follow = await makeLinkAdd(
  {
    type: 'follow',
    targetFid: 1,
  },
  dataOptions,
  ed25519Signer
);

const unfollow = await makeLinkRemove(
  {
    type: 'unfollow',
    targetFid: 1,
  },
  dataOptions,
  ed25519Signer
);
```
