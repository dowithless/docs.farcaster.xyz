---
title: Mejores Prácticas para Frames
---

# Mejores Prácticas para Frames

Aprende las mejores prácticas para construir excelentes experiencias con Frames.

::: info
Estas mejores prácticas asumen un conocimiento previo de Frames. Si buscas información general sobre cómo construir Frames, consulta estos recursos:

- [Comenzando](./getting-started)
- [Especificación de Frames](./spec)
- guías de [frames.js](https://framesjs.org/guides/create-frame) o [frog](https://frog.fm/concepts/overview)
  :::

## UI / UX

#### Sigue las Frame Interface Guidelines (FIG)

El [FIG](https://github.com/paradigmxyz/Fig) proporciona una guía exhaustiva que puede ayudarte a diseñar una gran experiencia para frames. Cubre todo, desde [temas fundamentales](https://github.com/paradigmxyz/Fig?tab=readme-ov-file#foundations) como diseño y tipografía hasta [mejores prácticas al trabajar con transacciones](https://github.com/paradigmxyz/Fig?tab=readme-ov-file#patterns).

#### Optimiza el rendimiento

Haz que tu frame responda al usuario lo más rápido posible. Revisa e implementa las [mejores prácticas de rendimiento](#performance).

#### Usa estilos y componentes reutilizables

Construir un conjunto de estilos y componentes reutilizables puede ayudarte a avanzar rápidamente y crear interfaces consistentes.

[FrogUI](https://frog.fm/ui) es una extensión del Framework Frog que proporciona un conjunto de componentes primitivos personalizables.

## Mejores Prácticas de Rendimiento

::: tip
No te preocupes por estas recomendaciones si recién estás comenzando o trabajando en un prototipo.

¡Recuerda, la optimización prematura es la raíz de todo mal!
:::

#### Usa imágenes en caché

Cuando sea posible, sirve imágenes en URLs estables con encabezados de caché apropiados, ya que esto puede reducir drásticamente el tiempo de renderizado.

#### Responde a las solicitudes lo más rápido posible

No realices cálculos de larga duración, como interactuar con un LLM, mientras el usuario espera que se cargue el siguiente Frame. Consulta [realizar cálculos de larga duración en segundo plano](#long-running).

#### Realiza cálculos de larga duración en segundo plano {#long-running}

Si tu Frame necesita realizar un cálculo de larga duración, como interactuar con un LLM o enviar una transacción onchain, debe hacerse en "segundo plano" respondiendo inmediatamente con un frame que indique al usuario que se está realizando una acción y permita al usuario actualizar para verificar el estado.

#### Usa una copia local de los datos de Farcaster {#local-farcaster-data}

Si tu frame necesita acceder a datos de Farcaster, considera [usar Shuttle para replicarlos en Postgres](/es/developers/guides/apps/replicate.md) para que puedan ser obtenidos localmente.

::: warning Advanced
Mantener una base de datos replicada implica un trabajo no trivial. Considera obtener datos de un proveedor como Neynar, Pinata o Airstack a menos que estés dispuesto a asumir este costo.
:::

#### Omite la verificación de la firma del mensaje del Frame cuando sea apropiado {#skip-verification}

Puede que no necesites verificar el mensaje del frame si los datos en el mensaje no resultan en la actualización o exposición de datos sensibles.

Por ejemplo, si tu frame devuelve datos analíticos públicos sobre el historial de casts de un usuario, confiar en los datos no verificados probablemente sea suficiente.

::: info Nota
Para frames de múltiples pasos, puede que algunas interacciones requieran verificación mientras que otras no.
:::

::: warning Ejercita precaución
Si tu frame depende del mensaje para permitir al usuario realizar una acción privilegiada o exponer datos sensibles, debes verificar el mensaje.
:::

#### Minimiza la latencia de dependencias externas como hubs

Esta es una generalización de tácticas como [omitir verificación](#skip-verification) y [usar una copia local de los datos de Farcaster](#local-farcaster-data).

Identifica todas las dependencias externas de tu servidor de frames. Para cada una de estas dependencias externas, pregúntate lo siguiente:

- ¿Se puede eliminar?
- ¿Se pueden almacenar en caché los datos?
- ¿Se puede ubicar junto a mi servidor de frames?
