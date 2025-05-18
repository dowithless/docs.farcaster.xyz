# Generar un feed cronológico para un usuario

Este ejemplo mostrará cómo leer datos de la red Farcaster utilizando el [hub-nodejs](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/hub-nodejs) oficial en TypeScript.

Crearemos un feed simple de casts a partir de una lista de FIDs y los mostraremos en orden cronológico inverso.

## Instalación

Crea un proyecto TypeScript vacío e instala el paquete hub-nodejs

```bash
yarn add @farcaster/hub-nodejs
```

## 1. Crear un cliente

Primero, configuremos algunas constantes y creemos un cliente para conectarnos al hub.

```typescript
import { getSSLHubRpcClient } from '@farcaster/hub-nodejs';

/**
 * Completa las siguientes constantes con tus propios valores
 */

const HUB_URL = 'hoyt.farcaster.xyz:2283'; // URL del Hub
const FIDS = [2, 3]; // IDs de usuario para obtener casts

// const client = getInsecureHubRpcClient(HUB_URL); // Usa esto si no estás usando SSL
const client = getSSLHubRpcClient(HUB_URL);
```

## 2. Almacenar en caché los fnames para los FIDs

Consulta los UserData para los FIDs proporcionados, para poder mostrar nombres de usuario amigables. Almacénalos en caché para más tarde.

```typescript
// Crea un mapeo de fids a fnames, que necesitaremos más tarde para mostrar mensajes
const fidToFname = new Map<number, string>();

const fnameResultPromises = FIDS.map((fid) =>
  client.getUserData({ fid, userDataType: UserDataType.USERNAME })
);
const fnameResults = await Promise.all(fnameResultPromises);

fnameResults.map((result) =>
  result.map((uData) => {
    if (isUserDataAddMessage(uData)) {
      const fid = uData.data.fid;
      const fname = uData.data.userDataBody.value;
      fidToFname.set(fid, fname);
    }
  })
);
```

## 3. Obtener casts de nivel superior

Consulta el hub para obtener todos los casts de cada FID, ordenados en orden cronológico inverso y luego filtra solo los casts de nivel superior.

```typescript
/**
 * Devuelve los casts de un usuario que no son respuestas a otros casts, en orden cronológico inverso.
 */
const getPrimaryCastsByFid = async (
  fid: number,
  client: HubRpcClient
): HubAsyncResult<CastAddMessage[]> => {
  const result = await client.getCastsByFid({
    fid: fid,
    pageSize: 10,
    reverse: true,
  });
  if (result.isErr()) {
    return err(result.error);
  }
  // Convierte Messages en Casts, no debería filtrar ningún mensaje
  const casts = result.value.messages.filter(isCastAddMessage);
  return ok(casts.filter((message) => !message.data.castAddBody.parentCastId));
};

// Obtén casts principales para cada fid
const castResultPromises = FIDS.map((fid) => getPrimaryCastsByFid(fid, client));
const castsResult = Result.combine(await Promise.all(castResultPromises));

if (castsResult.isErr()) {
  console.error('Error al obtener casts');
  console.error(castsResult.error);
  return;
}
```

## 4. Función para formatear un cast

Los datos brutos del cast no son muy legibles. Escribiremos una función para convertir la marca de tiempo a un formato legible y también resolver cualquier mención (solo almacenada como fids y su ubicación dentro del cast) a sus fnames.

```typescript
const castToString = async (
  cast: CastAddMessage,
  nameMapping: Map<number, string>,
  client: HubRpcClient
) => {
  const fname = nameMapping.get(cast.data.fid) ?? `${cast.data.fid}!`; // si el usuario no tiene un nombre de usuario configurado, usa su FID

  // Convierte la marca de tiempo a una cadena legible
  const unixTime = fromFarcasterTime(cast.data.timestamp)._unsafeUnwrap();
  const dateString = timeAgo.format(new Date(unixTime));

  const { text, mentions, mentionsPositions } = cast.data.castAddBody;
  const bytes = new TextEncoder().encode(text);

  const decoder = new TextDecoder();
  let textWithMentions = '';
  let indexBytes = 0;
  for (let i = 0; i < mentions.length; i++) {
    textWithMentions += decoder.decode(
      bytes.slice(indexBytes, mentionsPositions[i])
    );
    const result = await getFnameFromFid(mentions[i], client);
    result.map((fname) => (textWithMentions += fname));
    indexBytes = mentionsPositions[i];
  }
  textWithMentions += decoder.decode(bytes.slice(indexBytes));

  // Elimina saltos de línea del texto del mensaje
  const textNoLineBreaks = textWithMentions.replace(/(\r\n|\n|\r)/gm, ' ');

  return `${fname}: ${textNoLineBreaks}\n${dateString}\n`;
};
```

## 5. Ordenar e imprimir los casts

Finalmente, podemos ordenar los casts por marca de tiempo nuevamente (para que se intercalen correctamente) e imprimirlos.

```typescript
/**
 * Compara dos CastAddMessages por marca de tiempo, en orden cronológico inverso.
 */
const compareCasts = (a: CastAddMessage, b: CastAddMessage) => {
  if (a.data.timestamp < b.data.timestamp) {
    return 1;
  }
  if (a.data.timestamp > b.data.timestamp) {
    return -1;
  }
  return 0;
};

const sortedCasts = castsResult.value.flat().sort(compareCasts); // ordena casts por marca de tiempo
const stringifiedCasts = await Promise.all(
  sortedCasts.map((c) => castToString(c, fidToFname, client))
); // convierte casts a cadenas imprimibles

for (const outputCast of stringifiedCasts) {
  console.log(outputCast);
}
```

## Ejemplo completo

Consulta el ejemplo completo [aquí](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/hub-nodejs/examples/chron-feed)
