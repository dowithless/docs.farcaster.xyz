# Hubs

Los Hubs son una red distribuida de servidores que almacenan y validan datos de Farcaster.

Una computadora puede ejecutar software para convertirse en un Hub de Farcaster. Descargará datos onchain de Farcaster desde Ethereum y datos offchain de Farcaster desde otros Hubs. Cada Hub almacena una copia de todos los datos de Farcaster, los cuales pueden ser accedidos a través de una API.

Los Hubs te permiten leer y escribir datos en Farcaster, y cualquiera que construya una app de Farcaster necesitará comunicarse con uno. Cualquier persona puede ejecutar un Hub en su laptop o en un servidor en la nube. Una guía completa para configurar y ejecutar un Hub está disponible [aquí](https://www.thehubble.xyz).

## Diseño

Un Hub comienza sincronizando datos desde los contratos de Farcaster en la blockchain de Optimism. Se entera de cada cuenta de usuario y sus claves de cuenta.

El ciclo de vida de un mensaje de Farcaster luce así:

1. Alice crea un nuevo mensaje "¡Hola Mundo!".
2. Alice (o su app) firma el mensaje con una clave de cuenta.
3. Alice (o su app) sube el mensaje a un Hub.
4. El Hub verifica la validez del mensaje.
5. El Hub envía el mensaje a otros hubs pares mediante gossip.

![Hub](/assets/hub.png)

### Validación

El mensaje de Alice es validado verificando que tenga una firma válida de una de sus claves de cuenta. El Hub también asegura que el mensaje cumpla con los requisitos del tipo de mensaje. Por ejemplo, un mensaje público o "cast" debe tener menos de 320 bytes. Los requisitos de los tipos de mensaje están especificados en detalle en el [protocol spec](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md).

### Almacenamiento

El mensaje de Alice es luego verificado en busca de conflictos antes de ser almacenado en el Hub. Los conflictos pueden ocurrir por muchas razones:

1. El Hub ya tiene una copia del mensaje.
2. El Hub tiene un mensaje posterior de Alice eliminando este mensaje.
3. Alice solo ha pagado renta por 5000 casts, y este es su cast número 5001.

Los conflictos se resuelven determinísticamente y asincrónicamente usando CRDTs (Conflict-free Replicated Data Types). Por ejemplo, si Alice no tiene espacio para almacenar mensajes, su mensaje más antiguo será eliminado.

### Replicación

Los Hubs se sincronizan usando un proceso de dos fases: gossip y diff sync. Cuando un Hub recibe y almacena un mensaje, inmediatamente lo propaga (gossip) a sus pares. El gossip se realiza usando el protocolo gossipsub de libp2p y es lossy. Luego, los Hubs periódicamente seleccionan un par aleatorio y realizan un diff sync para encontrar mensajes perdidos. El proceso de diff sync compara merkle tries de hashes de mensajes para encontrar eficientemente mensajes perdidos.

### Consistencia

Se dice que los Hubs tienen consistencia eventual fuerte. Si un Hub se desconecta, se puede escribir en él y se recuperará de manera segura cuando vuelva a estar en línea. Esto es diferente a las blockchains donde un nodo desconectado no puede confirmar transacciones. La desventaja es que los mensajes pueden llegar desordenados. Por ejemplo, la respuesta de Bob a Alice podría aparecer antes de su mensaje "¡Hola Mundo!".

### Puntuación de Pares

Los Hubs monitorean a sus pares y puntúan su comportamiento. Si un par no acepta mensajes válidos, se queda atrás o propaga demasiado gossip, puede ser ignorado por sus pares.

### Implementaciones

- [Hubble](https://www.thehubble.xyz) - una implementación de Hub en TypeScript y Rust

## FAQ

**1. ¿Por qué debería ejecutar un Hub?**

Puedes necesitar un Hub si estás construyendo una app que quiera leer o escribir datos de Farcaster.

**2. ¿Hay recompensas por ejecutar un Hub?**

Farcaster no provee recompensas por los Hubs y no planea hacerlo.
