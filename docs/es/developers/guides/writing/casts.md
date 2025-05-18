# Crear casts

La creación de casts simples se cubre en el tutorial de [mensajes](./messages.md). Este tutorial aborda temas avanzados como menciones, embeds, emojis, respuestas y canales.

## Configuración

- Consulta las instrucciones de configuración en [creación de mensajes](./messages.md)

## Menciones

Los usuarios pueden ser etiquetados en un cast con una mención @username (ej. "¡Hola @bob!") lo que hace que los clientes envíen notificaciones.

Una mención no se incluye en el texto del cast. El fid objetivo y la posición de la mención en el texto se especifican en los arreglos `mentions` y `mentionPositions`, y son insertados en el cast por los clientes al momento de renderizar.

```typescript
/**
 * "@dwr y @v son grandes fans de @farcaster"
 */
const castWithMentions = await makeCastAdd(
  {
    text: ' y  son grandes fans de ',
    embeds: [],
    embedsDeprecated: [],
    mentions: [3, 2, 1],
    mentionsPositions: [0, 5, 22], // La posición en bytes (no caracteres)
  },
  dataOptions,
  ed25519Signer
);
```

## Embeds

Las URLs pueden ser embebidas en el cast, lo que indica a los clientes que muestren una vista previa.

Un cast puede tener hasta 2 embeds, cada uno de hasta 256 bytes de longitud. No se realiza ninguna otra validación en los embeds.

```typescript
/**
 * Un cast con una mención y un adjunto
 *
 * "¡Oye @dwr, mira esto!"
 */
const castWithMentionsAndAttachment = await makeCastAdd(
  {
    text: '¡Oye, mira esto!',
    embeds: [{ url: 'https://farcaster.xyz' }],
    embedsDeprecated: [],
    mentions: [3],
    mentionsPositions: [4],
  },
  dataOptions,
  ed25519Signer
);
```

## Emojis

Los emojis pueden incluirse directamente en el texto de un cast y ser renderizados por los clientes.

Dado que un carácter de emoji a menudo ocupa múltiples bytes pero se renderiza como un solo carácter, esto afecta cómo se deben calcular las posiciones de las menciones y la longitud del cast.

```typescript
/**
 * Un cast con emojis y menciones
 *
 * "🤓@farcaster puede mencionar inmediatamente después de un emoji"
 */
const castWithEmojiAndMentions = await makeCastAdd(
  {
    text: '🤓 puede mencionar inmediatamente después de un emoji',
    embeds: [],
    embedsDeprecated: [],
    mentions: [1],
    mentionsPositions: [4],
  },
  dataOptions,
  ed25519Signer
);
```

## Respuesta

Un cast puede ser una respuesta a otro cast, URL o NFT. Una respuesta a otro cast se trata como un hilo, mientras que una respuesta a una URL o NFT puede tratarse como un comentario o un [canal](#channels).

La propiedad opcional parentUrl puede establecerse como una URL o como un valor fid-hash del cast al que se responde, como se muestra en el ejemplo a continuación. Consulta [FIP-2](https://github.com/farcasterxyz/protocol/discussions/71) para más detalles sobre los tipos de respuesta.

```typescript
/**
 * Un cast que responde a una URL
 *
 * "Creo que este es un gran protocolo 🚀"
 */
const castReplyingToAUrl = await makeCastAdd(
  {
    text: 'Creo que este es un gran protocolo 🚀',
    embeds: [],
    embedsDeprecated: [],
    mentions: [],
    mentionsPositions: [],
    parentUrl: 'https://www.farcaster.xyz/',
  },
  dataOptions,
  ed25519Signer
);
```

## Canales

Un cast puede enviarse a un canal, lo que indica a los clientes que está relacionado con un tema específico. Los clientes pueden elegir usar estos metadatos de diferentes maneras, como agrupar casts de canales o recomendarlos a ciertos usuarios.

Un canal es simplemente un `parentURL` que puede ser una URL o NFT, que todos los clientes reconocen como canal por consenso informal. Aún no hay un acuerdo a nivel de protocolo sobre la lista de canales, pero la tabla `casts` en la base de datos del replicador puede usarse para obtener una lista de canales comúnmente utilizados.

```sql
/* Consulta para obtener una lista de canales */
select parent_url,
       count(*) as count
from casts
where parent_url is not null
group by parent_url
order by count desc;
```

```typescript
// Cast en el canal Farcaster
const channelCast = await makeCastAdd(
  {
    text: 'Me encanta farcaster',
    embeds: [],
    embedsDeprecated: [],
    mentions: [],
    mentionsPositions: [],
    parentUrl: 'https://www.farcaster.xyz/', // Esto es ilustrativo y no es una URL real de canal
  },
  dataOptions,
  ed25519Signer
);
```
