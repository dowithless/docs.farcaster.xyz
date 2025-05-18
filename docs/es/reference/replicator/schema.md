# Esquema

Las siguientes tablas se crean en la base de datos Postgres donde se almacenan los datos de los Hubs:

## chain_events

Todos los eventos onchain recibidos del flujo de eventos del hub se almacenan en esta tabla. Estos eventos representan cualquier acción onchain, incluyendo registros, transferencias, adiciones/eliminaciones de firmantes, alquileres de almacenamiento, etc. Los eventos nunca se eliminan (es decir, esta tabla es de solo añadir).

| Nombre de Columna | Tipo de Dato               | Descripción                                                                                                                         |
| ----------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at        | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no es lo mismo que la marca de tiempo del mensaje!)                             |
| block_timestamp   | `timestamp with time zone` | Marca de tiempo del bloque en el que se emitió este evento en UTC.                                                                  |
| fid               | `bigint`                   | FID del usuario que firmó el mensaje.                                                                                               |
| chain_id          | `bigint`                   | ID de la cadena.                                                                                                                    |
| block_number      | `bigint`                   | Número del bloque en el que se emitió este evento.                                                                                  |
| transaction_index | `smallint`                 | Índice de la transacción en el bloque.                                                                                              |
| log_index         | `smallint`                 | Índice del evento de log en el bloque.                                                                                              |
| type              | `smallint`                 | Tipo de evento de cadena.                                                                                                           |
| block_hash        | `bytea`                    | Hash del bloque donde se emitió este evento.                                                                                        |
| transaction_hash  | `bytea`                    | Hash de la transacción que desencadenó este evento.                                                                                 |
| body              | `json`                     | Representación JSON del cuerpo del evento de cadena (cambia de forma según el `type`).                                              |
| raw               | `bytea`                    | Bytes crudos que representan el `OnChainEvent` serializado [protobuf](https://protobuf.dev/).                                       |

## fids

Almacena todos los FIDs registrados en la red Farcaster.

| Nombre de Columna | Tipo de Dato               | Descripción                                                                                   |
| ----------------- | -------------------------- | --------------------------------------------------------------------------------------------- |
| fid               | `bigint`                   | FID del usuario (clave primaria)                                                              |
| created_at        | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no es lo mismo que la fecha de registro!) |
| updated_at        | `timestamp with time zone` | Cuándo se actualizó la fila por última vez.                                                   |
| registered_at     | `timestamp with time zone` | Marca de tiempo del bloque en el que el usuario fue registrado.                               |
| chain_event_id    | `uuid`                     | ID de la fila en la tabla `chain_events` correspondiente al registro inicial de este FID.     |
| custody_address   | `bytea`                    | Dirección que posee el FID.                                                                   |
| recovery_address  | `bytea`                    | Dirección que puede iniciar una recuperación para este FID.                                   |

## signers

Almacena todas las claves de cuenta (firmantes) registradas.

| Nombre de Columna     | Tipo de Dato               | Descripción                                                                                                                         |
| --------------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                    | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at            | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no es lo mismo que cuando se creó la clave en la red!)                          |
| updated_at            | `timestamp with time zone` | Cuándo se actualizó la fila por última vez.                                                                                         |
| added_at              | `timestamp with time zone` | Marca de tiempo del bloque donde se añadió este firmante.                                                                           |
| removed_at            | `timestamp with time zone` | Marca de tiempo del bloque donde se eliminó este firmante.                                                                          |
| fid                   | `bigint`                   | FID del usuario que autorizó este firmante.                                                                                         |
| requester_fid         | `bigint`                   | FID del usuario/app que solicitó este firmante.                                                                                     |
| add_chain_event_id    | `uuid`                     | ID de la fila en la tabla `chain_events` correspondiente a la adición de este firmante.                                             |
| remove_chain_event_id | `uuid`                     | ID de la fila en la tabla `chain_events` correspondiente a la eliminación de este firmante.                                         |
| key_type              | `smallint`                 | Tipo de clave.                                                                                                                      |
| metadata_type         | `smallint`                 | Tipo de metadatos.                                                                                                                  |
| key                   | `bytea`                    | Bytes de la clave pública.                                                                                                          |
| metadata              | `bytea`                    | Bytes de metadatos tal como se almacenan en la blockchain.                                                                          |

## username_proofs

Almacena todas las pruebas de nombre de usuario que se han visto. Esto incluye pruebas que ya no son válidas, que se eliminan suavemente mediante la columna `deleted_at`. Al consultar nombres de usuario, probablemente quieras consultar directamente la tabla `fnames`, en lugar de esta tabla.

| Nombre de Columna | Tipo de Dato               | Descripción                                                                                                                         |
| ----------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at        | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no es lo mismo que cuando se creó la clave en la red!)                          |
| updated_at        | `timestamp with time zone` | Cuándo se actualizó la fila por última vez.                                                                                         |
| timestamp         | `timestamp with time zone` | Marca de tiempo del mensaje de prueba.                                                                                              |
| deleted_at        | `timestamp with time zone` | Cuándo esta prueba fue revocada o invalidada de otro modo.                                                                          |
| fid               | `bigint`                   | FID al que pertenece el nombre de usuario en la prueba.                                                                             |
| type              | `smallint`                 | Tipo de prueba (ya sea fname o ENS).                                                                                                |
| username          | `text`                     | Nombre de usuario, p.ej. `dwr` si es un fname, o `dwr.eth` si es un nombre ENS.                                                     |
| signature         | `bytea`                    | Firma de la prueba.                                                                                                                 |
| owner             | `bytea`                    | Dirección de la billetera que posee el nombre ENS, o la billetera que proporcionó la firma de la prueba.                            |

## fnames

Almacena todos los nombres de usuario que están actualmente registrados. Ten en cuenta que en caso de que un nombre de usuario sea dado de baja, la fila se elimina suavemente mediante la columna `deleted_at` hasta que se registre un nuevo nombre de usuario para el FID dado.

| Nombre de Columna | Tipo de Dato               | Descripción                                                                                                                         |
| ----------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at        | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no es lo mismo que cuando se creó la clave en la red!)                          |
| updated_at        | `timestamp with time zone` | Cuándo se actualizó la fila por última vez.                                                                                         |
| registered_at     | `timestamp with time zone` | Marca de tiempo del mensaje de prueba del nombre de usuario.                                                                        |
| deleted_at        | `timestamp with time zone` | Cuándo la prueba fue revocada o el fname fue dado de baja de este usuario de otro modo.                                             |
| fid               | `bigint`                   | FID al que pertenece el nombre de usuario.                                                                                          |
| type              | `smallint`                 | Tipo de nombre de usuario (ya sea fname o ENS).                                                                                     |
| username          | `text`                     | Nombre de usuario, p.ej. `dwr` si es un fname, o `dwr.eth` si es un nombre ENS.                                                     |

## messages

Todos los mensajes Farcaster recuperados del hub se almacenan en esta tabla. Los mensajes nunca se eliminan, solo se eliminan suavemente (es decir, se marcan como eliminados pero no se eliminan realmente de la BD).

| Nombre de Columna | Tipo de Dato               | Descripción                                                                                                                         |
| ----------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at        | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no es lo mismo que la marca de tiempo del mensaje!)                             |
| updated_at        | `timestamp with time zone` | Cuándo se actualizó la fila por última vez.                                                                                         |
| timestamp         | `timestamp with time zone` | Marca de tiempo del mensaje en UTC.                                                                                                 |
| deleted_at        | `timestamp with time zone` | Cuándo el mensaje fue eliminado por el hub (p.ej. en respuesta a un mensaje `CastRemove`, etc.)                                     |
| pruned_at         | `timestamp with time zone` | Cuándo el mensaje fue podado por el hub.                                                                                            |
| revoked_at        | `timestamp with time zone` | Cuándo el mensaje fue revocado por el hub debido a la revocación del firmante que firmó el mensaje.                                 |
| fid               | `bigint`                   | FID del usuario que firmó el mensaje.                                                                                               |
| type              | `smallint`                 | Tipo de mensaje.                                                                                                                    |
| hash_scheme       | `smallint`                 | Esquema de hash del mensaje.                                                                                                        |
| signature_scheme  | `smallint`                 | Esquema de firma del mensaje.                                                                                                       |
| hash              | `bytea`                    | Hash del mensaje.                                                                                                                   |
| signature         | `bytea`                    | Firma del mensaje.                                                                                                                  |
| signer            | `bytea`                    | Firmante utilizado para firmar este mensaje.                                                                                        |
| body              | `json`                     | Representación JSON del cuerpo del mensaje.                                                                                         |
| raw               | `bytea`                    | Bytes crudos que representan el mensaje serializado [protobuf](https://protobuf.dev/).                                              |

## casts

Representa un cast creado por un usuario.

| Nombre de Columna  | Tipo de Dato               | Descripción                                                                                                                         |
| ------------------ | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                 | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at         | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no es lo mismo que la marca de tiempo del mensaje!)                             |
| updated_at         | `timestamp with time zone` | Cuándo se actualizó la fila por última vez.                                                                                         |
| timestamp          | `timestamp with time zone` | Marca de tiempo del mensaje en UTC.                                                                                                 |
| deleted_at         | `timestamp with time zone` | Cuándo el cast fue considerado eliminado/revocado/podado por el hub (p.ej. en respuesta a un mensaje `CastRemove`, etc.)            |
| fid                | `bigint`                   | FID del usuario que firmó el mensaje.                                                                                               |
| parent_fid         | `bigint`                   | Si este cast fue una respuesta, el FID del autor del cast padre. `null` en caso contrario.                                          |
| hash               | `bytea`                    | Hash del mensaje.                                                                                                                   |
| root_parent_hash   | `bytea`                    | Si este cast fue una respuesta, el hash del cast original en la cadena de respuestas. `null` en caso contrario.                     |
| parent_hash        | `bytea`                    | Si este cast fue una respuesta, el hash del cast padre. `null` en caso contrario.                                                   |
| root_parent_url    | `text`                     | Si este cast fue una respuesta, entonces la URL a la que el cast original en la cadena de respuestas estaba respondiendo.           |
| parent_url         | `text`                     | Si este cast fue una respuesta a una URL (p.ej. un NFT, una URL web, etc.), la URL. `null` en caso contrario.                       |
| text               | `text`                     | El texto crudo del cast con las menciones eliminadas.                                                                               |
| embeds             | `json`                     | Array de URLs o IDs de cast que se incrustaron con este cast.                                                                       |
| mentions           | `json`                     | Array de FIDs mencionados en el cast.                                                                                               |
| mentions_positions | `json`                     | Desplazamientos de bytes UTF8 de los FIDs mencionados en el cast.                                                                   |

## reactions

Representa a un usuario reaccionando (gustando o recasteando) contenido.

| Nombre de Columna | Tipo de Dato               | Descripción                                                                                                                         |
| ----------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at        | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no es lo mismo que la marca de tiempo del mensaje!)                             |
| updated_at        | `timestamp with time zone` | Cuándo se actualizó la fila por última vez.                                                                                         |
| timestamp         | `timestamp with time zone` | Marca de tiempo del mensaje en UTC.                                                                                                 |
| deleted_at        | `timestamp with time zone` | Cuándo la reacción fue considerada eliminada por el hub (p.ej. en respuesta a un mensaje `ReactionRemove`, etc.)                    |
| fid               | `bigint`                   | FID del usuario que firmó el mensaje.                                                                                               |
| target_cast_fid   | `bigint`                   | Si el objetivo fue un cast, el FID del autor del cast. `null` en caso contrario.                                                    |
| type              | `smallint`                 | Tipo de reacción.                                                                                                                   |
| hash              | `bytea`                    | Hash del mensaje.                                                                                                                   |
| target_cast_hash  | `bytea`                    | Si el objetivo fue un cast, el hash del cast. `null` en caso contrario.                                                             |
| target_url        | `text`                     | Si el objetivo fue una URL (p.ej. NFT, una URL web, etc.), la URL. `null` en caso contrario.                                        |

## links

Representa un enlace entre dos FIDs (p.ej. un seguimiento, suscripción, etc.)

| Nombre de Columna | Tipo de Dato               | Descripción                                                                                                                         |
| ----------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at        | `timestamp with time zone` | Cuándo se creó la fila por primera vez en esta BD (¡no cuando se creó el enlace en la red!)                                         |
| updated_at        | `timestamp with time zone` | Cuándo se actualizó la fila por última vez                                                                                          |
| timestamp         | `timestamp with time zone` | Marca de tiempo del mensaje en UTC.                                                                                                 |
| deleted_at        | `timestamp with time zone` | Cuándo el enlace fue considerado eliminado por el hub (p.ej. en respuesta a un mensaje `LinkRemoveMessage`, etc.)                   |
| fid               | `bigint`                   | Farcaster ID (el ID de usuario).                                                                                                    |
| target_fid        | `bigint`                   | Farcaster ID del usuario objetivo.                                                                                                  |
| display_timestamp | `timestamp with time zone` | Cuándo se actualizó la fila por última vez.                                                                                         |
| type              | `string`                   | Tipo de conexión entre usuarios, p.ej. `follow`.                                                                                    |
| hash              | `bytea`                    | Hash del mensaje.                                                                                                                   |

## verifications

Representa a un usuario verificando algo en la red. Actualmente, la única verificación es demostrar la propiedad de una dirección de billetera Ethereum.

| Nombre de Columna | Tipo de Dato               | Descripción                                                                                                                         |
| ----------------- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| id                | `uuid`                     | Identificador genérico específico de esta BD (también conocido como [clave sustituta](https://en.wikipedia.org/wiki/Surrogate_key)) |
| created_at        | `timestamp with time zone` | Cuándo se creó la                                                                                                                   |
