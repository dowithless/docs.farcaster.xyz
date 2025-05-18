---
title: Temas Avanzados sobre Frames
---

# Temas Avanzados

Temas avanzados para construir Farcaster Frames sofisticados.

#### Hacer dinámica la imagen inicial del Frame

Cuando un Frame se comparte en un cast, los metadatos generalmente se extraen y almacenan en caché para que puedan mostrarse en los feeds de los usuarios sin carga adicional. Esto significa que la URL establecida en el frame inicial siempre se renderizará.

Para hacer dinámica la imagen inicial, necesitarás:

- servir una imagen dinámica en una URL estática
- configurar un encabezado de caché apropiado

Por ejemplo, imagina que quieres construir un frame donde la imagen inicial muestre el precio actual de ETH. Para el frame inicial, establecerías una URL de imagen estática como `https://example.xyz/eth-price.png`. Cuando se haga una solicitud GET a este endpoint:

- el servidor obtendrá el último precio de ETH desde una caché
- renderizará una imagen usando una herramienta como [Vercel OG](https://vercel.com/docs/functions/og-image-generation) y la devolverá
- establecerá el siguiente encabezado: `Cache-Control: public, immutable, no-transform, max-age=60`

Recomendamos establecer un `max-age` distinto de cero para que la imagen pueda almacenarse en caché y servirse desde CDNs; de lo contrario, los usuarios verán una imagen gris en su feed mientras la imagen dinámica se genera y carga. El tiempo exacto depende de tu aplicación, pero opta por el tiempo más largo que mantenga la imagen razonablemente actualizada.

#### Evitar almacenar en caché imágenes de error al generar imágenes dinámicas en URLs estáticas

Si tienes una URL estática que genera una imagen dinámica y utilizas una imagen de respaldo para casos en los que no puedas generar la imagen, debes establecer `max-age=0` en el encabezado `Cache-Control` para que no se almacene en caché.

Por ejemplo, supongamos que generas una imagen dinámica en `/img/eth-price` que muestra un gráfico de 24 horas del precio de ETH. Normalmente, querrás que esta imagen se almacene en caché durante 5 minutos. Sin embargo, si los datos del precio de ETH no están disponibles y renderizas una imagen de respaldo, no querrás que la solicitud se almacene en caché para poder intentarlo nuevamente en solicitudes posteriores.

#### Persistencia de datos

[Vercel KV](https://vercel.com/docs/storage/vercel-kv) y [Cloudflare Workers KV](https://developers.cloudflare.com/kv/) son opciones convenientes para agregar una capa de persistencia simple a tu servidor de Frames.
