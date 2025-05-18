# Especificación de Acciones

Las acciones permiten a los desarrolladores crear botones personalizados que los usuarios pueden instalar en su barra de acciones en cualquier aplicación de Farcaster. Piensa en ellas como extensiones de navegador, pero para casts.

Al igual que los [Frames](/es/developers/frames/spec), las acciones son un estándar abierto para extender los casts con nuevos tipos de interacciones. Las acciones y los frames son componibles, lo que permite a los desarrolladores crear aplicaciones interactivas y autenticadas que funcionan en todos los clientes de Farcaster.

Hasta ahora, los desarrolladores han utilizado acciones para agregar funciones como traducción, herramientas de moderación y propinas a los clientes de Farcaster.

## Definición de acciones

Una acción es una API web que reside en una URL en un servidor web. Nos referiremos a este servidor web como el "servidor de acciones".

El servidor de acciones debe definir dos rutas:

- Una ruta GET que devuelve metadatos sobre la acción
- Una ruta POST para manejar las solicitudes de acción

Los clientes de Farcaster cargan la ruta GET para obtener información sobre la acción y realizan solicitudes a la ruta POST cuando los usuarios hacen clic en el botón de acción en el feed.

## Ruta de metadatos

Los servidores de acciones deben responder a una solicitud HTTP GET a su URL de metadatos con un 200 OK y un cuerpo JSON en el siguiente formato:

```json
{
  "name": "Recordatorio en 10 días",
  "icon": "lightbulb",
  "description": "Recibe un recordatorio automático de @remindbot en 10 días.",
  "aboutUrl": "https://remindbot.example.com/remind/about",
  "action": {
    "type": "post",
    "postUrl": "https://remindbot.example.com/actions/remind"
  }
}
```

### Propiedades

| Clave            | Descripción                                                                                                                                                                                |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `name`           | Nombre de la acción, que puede tener hasta 30 caracteres.                                                                                                                                  |
| `icon`           | ID del icono de la acción. Consulta [Iconos válidos](#valid-icons) para ver una lista de iconos admitidos.                                                                                 |
| `description`    | Una breve descripción de hasta 80 caracteres.                                                                                                                                              |
| `aboutUrl`       | Enlace externo opcional a una página "acerca de". Solo inclúyelo si no puedes describir completamente tu acción usando el campo de descripción. Debe ser protocolo `http://` o `https://`. |
| `action.type`    | Tipo de acción. Debe ser `'post'`.                                                                                                                                                         |
| `action.postUrl` | URL opcional del manejador de acciones. Si no se proporciona, los clientes enviarán POST a la misma URL que la ruta de metadatos de la acción.                                             |

## Ruta del manejador

Cuando un usuario hace clic en un botón de acción de cast en el feed, los clientes realizan una solicitud POST al manejador de acciones con un mensaje firmado. Las acciones utilizan el mismo formato de [mensaje de firma de Frame](/es/developers/frames/spec#frame-signature-packet) que los frames de Farcaster.

En este mensaje:

- El `frame_url` se establece en el `postUrl` de la acción de cast.
- El `button_index` se establece en `1`.
- El `cast_id` es el ID del cast desde el cual el usuario inició la acción.

Los servidores de acciones pueden responder con un mensaje breve, una URL de frame o un error.

### Tipo de respuesta de mensaje

El tipo de respuesta de mensaje muestra un mensaje breve y un enlace externo opcional. Es adecuado para interacciones simples de un solo paso.

![Acciones de mensaje](/assets/actions/message_type.png)

Un servidor de acciones puede devolver 200 OK y un cuerpo JSON en el siguiente formato para mostrar un mensaje:

```json
{
  "type": "message",
  "message": "¡Recordatorio guardado!",
  "link": "https://remindbot.example.com/reminders/1"
}
```

**Propiedades**

| Clave     | Descripción                                                                                                                                             |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `type`    | Debe ser `message`.                                                                                                                                     |
| `message` | Un mensaje breve, menos de 80 caracteres.                                                                                                               |
| `link`    | Una URL opcional. Debe ser protocolo `http://` o `https://`. Si está presente, los clientes deben mostrar el mensaje como un enlace externo a esta URL. |

### Tipo de respuesta de frame

El tipo de respuesta de frame permite que las acciones de cast muestren un [Frame](/es/developers/frames/spec). Es útil para interacciones complejas de múltiples pasos.

![Acciones de frame](/assets/actions/frame_type.png)

Un servidor de acciones puede devolver 200 OK y un cuerpo JSON en el siguiente formato para mostrar un frame:

```json
{
  "type": "frame",
  "frameUrl": "https://remindbot.example.com/frame"
}
```

**Propiedades**

| Clave      | Descripción                                                                                                                                   |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `type`     | Debe ser `frame`.                                                                                                                             |
| `frameUrl` | URL del frame a mostrar. Cadena de 256 bytes. Los clientes deben mostrar el frame en un contexto especial, como un modal o una hoja inferior. |

### Tipo de respuesta de error

Un servidor de acciones puede devolver una respuesta 4xx con un mensaje para mostrar un error:

```json
{
  "message": "Algo salió mal."
}
```

**Propiedades**

| Clave     | Descripción                                        |
| --------- | -------------------------------------------------- |
| `message` | Un mensaje de error breve, menos de 80 caracteres. |

## Implementación del cliente

### Agregar acciones

Los clientes deben permitir a los usuarios habilitar acciones de cast haciendo clic en una URL de enlace profundo. Por ejemplo, en Warpcast:

```
https://warpcast.com/~/add-cast-action?url=https%3A%2F%2Fremindbot.example.com%2Fremind
```

Cuando un usuario habilita una acción de cast, los clientes deben:

- Cargar la URL y analizar sus parámetros de consulta para la ruta de metadatos de la acción.
- Hacer GET a la URL de metadatos de la acción para recuperar los metadatos.
- Validar y sanitizar todos los datos.
- Guardar la acción descubierta en nombre del usuario.
- Actualizar los metadatos para acciones guardadas existentes.
- Permitir al usuario eliminar acciones no deseadas o no utilizadas.

Los clientes deben mostrar una pantalla de confirmación que brinde al usuario más contexto sobre lo que hace la acción. Esta pantalla debe advertir al usuario si instalar esta acción reemplazará una acción anterior.

### Mostrar acciones

Los clientes deben mostrar acciones junto con los casts y permitir al usuario hacer clic/toquetear para interactuar.

#### Manejo de clics

Cuando el usuario hace clic en una acción, el cliente debe hacer una solicitud POST al `postUrl` de la acción con un mensaje de frame firmado.

En este mensaje, los clientes deben:

- Establecer `frame_url` en el `postUrl` de la acción de cast.
- Establecer `button_index` en `1`.
- Establecer `cast_id` en el ID del cast desde el cual el usuario inició la acción.
- Por lo demás, seguir el formato existente para los mensajes de Frame.

#### Mostrar respuestas

El servidor de acciones puede enviar una respuesta de texto breve, un frame o un error a los clientes.

Cuando un cliente recibe una respuesta de acción, debe:

1. Validar el formato de la respuesta:
   - Tipo frame:
     - debe incluir `frameUrl`
     - `frameUrl` debe comenzar con `https://`
   - Tipo message:
     - debe incluir una cadena `message` < 80 caracteres
2. Si la respuesta es de tipo message o error, mostrarla.
3. Si la respuesta es de tipo frame, enviar POST de un mensaje de Frame al `frameUrl` devuelto:
   - Establecer `buttonIndex` en 1
   - Establecer `castId` en el cuerpo del mensaje de Frame al ID del cast que se hizo clic
4. Analizar la respuesta como un Frame y mostrarla en un contexto especial, como una hoja inferior o un modal

Los clientes deben permitir al usuario cerrar el Frame una vez que se complete la interacción.

## Referencia

#### Iconos válidos

- number
- search
- image
- alert
- code
- meter
- ruby
- video
- filter
- stop
- plus
- info
- check
- book
- question
- mail
- home
- star
- inbox
- lock
- eye
- heart
- unlock
- play
- tag
- calendar
- database
- hourglass
- key
- gift
- sync
- archive
- bell
- bookmark
- briefcase
- bug
- clock
- credit-card
- globe
- infinity
- light-bulb
- location
- megaphone
- moon
- note
- pencil
- pin
- quote
- reply
- rocket
- shield
- stopwatch
- tools
- trash
- comment
- gear
- file
- hash
- square
- sun
- zap
- sign-out
- sign-in
- paste
- mortar-board
- history
- plug
- bell-slash
- diamond
- id-badge
- person
- smiley
- pulse
- beaker
- flame
- people
- person-add
- broadcast
- graph
- shield-check
- shield-lock
- telescope
- webhook
- accessibility
- report
- verified
- blocked
- bookmark-slash
- checklist
- circle-slash
- cross-reference
- dependabot
- device-camera
- device-camera-video
- device-desktop
- device-mobile
- dot
- eye-closed
- iterations
- key-asterisk
- law
- link-external
- list-ordered
- list-unordered
- log
- mention
- milestone
- mute
- no-entry
- north-star
- organization
- paintbrush
- paper-airplane
- project
- shield-x
- skip
- squirrel
- stack
- tasklist
- thumbsdown
- thumbsup
- typography
- unmute
- workflow
- versions
