---
title: Introducción a Frames
---

# Introducción

::: info ¿No estás listo para construir?
Si prefieres aprender más sobre Frames antes de construir uno, salta directamente a la [Especificación de Frames](./spec).
:::

Usemos Frog para ir de 0 a 1 en menos de un minuto. Al final de esto tendremos:

- un servidor de frames con seguridad de tipos y un frame básico
- una herramienta para interactuar y depurar nuestro Frame localmente
- nuestro servidor desplegado en internet público

### Inicialización vía CLI

Para comenzar, crea un nuevo proyecto:

::: code-group

```ts [npm]
npm init frog -t vercel
```

```ts [yarn]
yarn create frog -t vercel
```

```ts [bun]
bunx create-frog -t vercel
```

```ts [pnpm]
pnpm create frog -t vercel
```

Completa las indicaciones y sigue las instrucciones:

```
bun install // instalar dependencias
bun run dev // iniciar servidor de desarrollo
```

::: info
Esta guía usa Frog + Vercel, pero los frames pueden construirse y desplegarse de muchas formas. Puedes explorar alternativas populares como [frame.js](https://framesjs.org/) en la página [Recursos para Desarrolladores de Frames](./resources).
:::

### Abre la vista previa

Ahora que tienes un servidor de frames en funcionamiento, navega a `http://localhost:5174/api/dev` para interactuar con tu frame en la vista previa de [Devtools](https://frog.fm/concepts/devtools).

Deberías ver un frame que dice "¡Bienvenido!" y tiene tres botones: manzanas, naranjas y plátanos. Haz clic en cualquiera de los botones y el frame se actualizará con un mensaje que repite tu elección.

![Vista previa de Frog Frame](./frog_frame_preview.png)

### Despliega el frame

::: info
Este proyecto fue inicializado para despliegue con Vercel, ya que es una opción popular y sencilla. Si prefieres desplegar tu Frame de otra manera, consulta la [documentación de Plataformas Frog](https://frog.fm/platforms/bun).
:::

Para distribuir tu frame, tu servidor necesitará estar alojado en algún lugar de internet. Siéntete libre de saltar este paso y volver más tarde.

::: code-group

```ts [npm]
npm run deploy
```

```ts [yarn]
yarn run deploy
```

```ts [bun]
bunx run deploy
```

```ts [pnpm]
pnpm run deploy
```

Completa las indicaciones. Una vez que tu frame esté desplegado, puedes probarlo de extremo a extremo usando el [Validador de Frames de Warpcast](https://warpcast.com/~/developers/frames-legacy).

::: info
Asegúrate de ingresar la URL completa del frame. Para proyectos en Vercel, esta URL por defecto se encuentra en `https://<dominio>/api`.
:::

## Próximos pasos

Aquí tienes algunos pasos siguientes ahora que has inicializado y desplegado tu primer proyecto de Frames:

- Lee la [documentación de Conceptos de Frog](https://frog.fm/concepts/overview) para aprender a construir un [frame de múltiples pasos](https://frog.fm/concepts/routing) sofisticado.
- Prueba [inicializar un proyecto con frames.js](https://framesjs.org/guides/create-frame) y revisa sus extensas [guías](https://framesjs.org/guides/create-frame) y [ejemplos](https://framesjs.org/examples/basic).
- Descubre más recursos para [construir y aprender sobre frames](./resources).
- Revisa nuestras [mejores prácticas](./best-practices) para construir una gran experiencia con Frames.
