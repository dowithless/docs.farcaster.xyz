# Canales

Un canal es un espacio público para que tu comunidad tenga conversaciones en torno a un tema.

Crear un canal inicia un nuevo feed para tu comunidad. Las personas pueden unirse, publicar (cast) y encontrar a otras personas interesantes. Genera conversaciones que de otro modo no ocurrirían en el feed principal.

:::warning Experimental Feature
Los canales están siendo prototipados en Warpcast y no son totalmente compatibles con el protocolo Farcaster. Podrían integrarse al protocolo en el futuro si la función se considera exitosa o podrían eliminarse por completo.
:::

## Alojamiento de Canales

Cualquiera puede crear un canal como anfitrión pagando una tarifa en Warpcast y eligiendo un nombre para el canal. El nombre debe tener menos de 16 caracteres y solo puede contener letras minúsculas y números. El creador de un canal se llama anfitrión y puede invitar a otros coanfitriones para operar el canal. Los anfitriones tienen privilegios especiales como:

1. Definir "normas del canal" que todos deben aceptar al unirse.
2. Fijar u ocultar publicaciones (casts) en un canal.
3. Bloquear a otros usuarios para que no publiquen en su canal.
4. Establecer una imagen del canal, descripción y otros metadatos.

Los metadatos del canal no son parte del protocolo y se almacenan en Warpcast mientras los canales están en etapa experimental.

## Publicar en Canales

Cualquiera puede publicar en un canal usando Warpcast y seleccionando el canal al crear la publicación. Warpcast establece automáticamente el `parentUrl` de la publicación en `https://warpcast.com/~/channel/<nombre>`. Una publicación se considera "en un canal" si su parentUrl es el URI del canal o otra publicación que está "en un canal".

Las publicaciones en canales son parte del protocolo y se almacenan en los hubs. Usando un replicador, puedes obtener todas las publicaciones en un canal filtrando el campo `parentUrl` por la URL FIP-2 del canal.

## Seguir Canales

Cualquiera puede seguir un canal como si fuera un usuario. Un usuario verá las publicaciones de un canal seguido en su feed principal cuando use Warpcast.

Los seguimientos de canales no son parte del protocolo y se almacenan en Warpcast mientras los canales están en etapa experimental.

## Visibilidad de las Publicaciones

Si un usuario publica en un canal, Warpcast hará lo siguiente:

1. Siempre enviará las publicaciones a los feeds principales de cualquier usuario que siga el canal.
2. Generalmente enviará las publicaciones a los feeds principales de cualquier usuario que siga al autor.

La determinación para (2) se basa en las preferencias del usuario, el contenido del canal y otros datos del gráfico social. Este algoritmo aún se está ajustando y se documentará una vez que esté estable.

## Política de Uso

Warpcast puede eliminar tu canal y NO reembolsará tus warps si:

1. Tu perfil o canal suplanta a alguien.
2. Acaparas un canal sin usarlo.
3. Violas los términos y condiciones de Warpcast o las reglas de la tienda de aplicaciones.

## Preguntas Frecuentes

**¿Por qué se permite a los anfitriones de canales ocultar y prohibir? ¿No es esto censura?**

Los canales no son espacios públicos sin regulación, son propiedad de sus creadores y están moderados por ellos. Siempre eres libre de iniciar tu propio canal en cualquier momento con sus propias reglas.

**¿Por qué hay una tarifa para crear canales?**

La tarifa disuade a las personas de acaparar nombres cortos y no usar los canales.

**¿Cuál es el beneficio de crear un canal?**

Iniciar un canal también ayuda a hacer crecer tu audiencia:

1. Warpcast enviará a tus seguidores una notificación sobre tu canal.
2. Tu canal será promocionado a usuarios que siguen canales similares.
3. Los usuarios que sigan tu canal verán las publicaciones del canal en su feed principal.

## Recursos

### APIs

- [APIs de Canales de Warpcast](../../reference/warpcast/api.md) - obtener una lista de todos los canales conocidos
