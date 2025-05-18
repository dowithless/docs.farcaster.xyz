# Mensajes

Las cuentas de Farcaster interactúan firmando y publicando mensajes. Alice puede crear un mensaje que diga "_Hola @bob_" y firmarlo con su clave.

Los mensajes se almacenan en una red peer-to-peer de nodos. Un nodo en la red Farcaster se llama Hub, y cada Hub almacena una copia de toda la red. Un usuario puede publicar un mensaje en un Hub y este se propagará a toda la red en unos segundos. El formato compacto de mensajes de Farcaster y su modelo de consistencia eventual permiten que esta arquitectura escale a millones de usuarios.

Una cuenta puede generar una [clave](./accounts.md#adding-account-keys) y dársela a una app que puede usarla para firmar mensajes. Los usuarios pueden usar múltiples apps con la misma cuenta, y cada aplicación puede tener su propia clave. Separar las claves de firma de las claves de propiedad ayuda a mantener la cuenta segura.

## Tipos

Las cuentas pueden publicar cinco tipos diferentes de mensajes en la red:

| Tipo          | Descripción                                 | Ejemplo                          |
| ------------- | ------------------------------------------- | -------------------------------- |
| Casts         | Mensajes públicos visibles para cualquiera. | "¡Hola mundo!"                   |
| Reactions     | Una relación entre una cuenta y un cast.    | A Alice le gustó el cast de Bob. |
| Links         | Una relación entre dos cuentas.             | Alice sigue a Bob.               |
| Profile Data  | Metadatos sobre la cuenta.                  | Foto de perfil, nombre.          |
| Verifications | Una prueba de propiedad de algo.            | Una dirección de Ethereum.       |

## Almacenamiento

Una cuenta debe pagar renta para mantener sus mensajes en la red Farcaster. Cobrar renta evita que los usuarios spameen la red.

Una cuenta puede rentar una unidad de almacenamiento haciendo una transacción onchain al Storage Registry. Una unidad de almacenamiento cuesta $7 actualmente, dura un año y permite a cada cuenta almacenar cierto número de mensajes de cada tipo. Los límites actuales para cada tipo son:

- 5000 Casts
- 2500 Reactions
- 2500 Links
- 50 Profile Data
- 50 Verifications

Si una cuenta excede su límite para un tipo de mensaje, el mensaje más antiguo se elimina para hacer espacio al nuevo. El usuario puede seguir usando la red sin pagar más almacenamiento y los Hubs pueden mantener la carga de almacenamiento bajo control. Una cuenta siempre puede comprar más almacenamiento para aumentar sus límites.

Una cuenta que deje expirar su almacenamiento puede perder todos sus mensajes. Hay un período de gracia de 30 días después de que expire una unidad de almacenamiento durante el cual la cuenta debe renovar o perderá sus mensajes.

El precio y tamaño de cada unidad de almacenamiento se recalcula periódicamente para balancear el crecimiento y calidad de la red. Ver [FIP-6](https://github.com/farcasterxyz/protocol/discussions/98) para más detalles.

## Eliminación

Una cuenta puede eliminar mensajes en cualquier momento publicando un mensaje de eliminación correspondiente. El mensaje de eliminación borrará el contenido del mensaje original, dejando una tumba en su lugar. Un mensaje eliminado seguirá contando para el límite de almacenamiento de la cuenta hasta que expire al ser desplazado por un mensaje nuevo.

## Marcas de tiempo

Los mensajes tienen marcas de tiempo que cuentan segundos desde el Epoch de Farcaster, que comenzó el `1 de enero de 2021 00:00:00 UTC`. Usar un epoch reciente hace que las marcas de tiempo y mensajes sean mucho más pequeños, lo cual es importante para la red.

Las marcas de tiempo no están verificadas y pueden ser antedatadas por los usuarios, similar a una entrada de blog. No pueden estar más de 15 minutos en el futuro, ya que la red rechazará tales mensajes.

## Recursos

### Especificaciones

- [Mensajes](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md#2-message-specifications) - la unidad atómica de cambio en Farcaster
- [CRDTs](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md#31-crdts) - reglas para mantener los mensajes sincronizados en la red
- [Storage Registry](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md#13-storage-registry) - contrato para adquirir unidades de almacenamiento

### APIs

- [Get Casts](../../reference/hubble/httpapi/casts) - obtener los casts de una cuenta desde un hub
- [Get Reactions](../../reference/hubble/httpapi/reactions) - obtener las reactions de una cuenta desde un hub
- [Get Links](../../reference/hubble/httpapi/links) - obtener los links o follows de una cuenta desde un hub
- [Get UserData](../../reference/hubble/httpapi/userdata) - obtener los datos de perfil de una cuenta desde un hub
- [Submit Message](../../reference/hubble/httpapi/message#submitmessage) - transmitir un mensaje a la red de hubs
- [Validate Message](../../reference/hubble/httpapi/message#validatemessage) - verificar la autenticidad de un mensaje con un hub
- [Storage Registry](../../reference/contracts/reference/storage-registry) - Adquirir o verificar unidades de almacenamiento para una cuenta

### Tutoriales

- [Obtener casts](../../developers/guides/querying/fetch-casts) - Obtener los casts de una cuenta desde un hub.
- [Obtener perfil](../../developers/guides/querying/fetch-profile) - Obtener el perfil de una cuenta desde un hub.
- [Crear tipos comunes de mensajes](../../developers/guides/writing/messages) - Crear casts, links, reactions y userdata.
- [Crear casts con funciones avanzadas](../../developers/guides/writing/casts) - Crear casts con embeds, emojis y menciones.
