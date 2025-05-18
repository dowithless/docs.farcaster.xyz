---
title: URLs de Intenciones en Warpcast
---

# URLs de Intenciones

## URLs de Intención para Publicaciones (Casts)

Las intenciones de Warpcast permiten a los desarrolladores dirigir a usuarios autenticados a un compositor de publicaciones pre-rellenado.

> [!IMPORTANT]
> Si estás construyendo una Mini App y deseas solicitar al usuario que redacte una publicación, utiliza la acción [composeCast](https://miniapps.farcaster.xyz/docs/sdk/actions/compose-cast)
> del SDK de Mini Apps.

#### Redactar con texto de publicación

```
https://warpcast.com/~/compose?text=Hello%20world!
```

#### Redactar con texto de publicación y un embed

```
https://warpcast.com/~/compose?text=Hello%20world!&embeds[]=https://farcaster.xyz
```

#### Redactar con texto de publicación que incluye menciones y dos embeds

```
https://warpcast.com/~/compose?text=Hello%20@farcaster!&embeds[]=https://farcaster.xyz&embeds[]=https://github.com/farcasterxyz/protocol
```

#### Redactar con texto de publicación en un canal específico

```
https://warpcast.com/~/compose?text=Hello%20world!&channelKey=farcaster
```

#### Responder con texto de publicación a un cast con hash

```
https://warpcast.com/~/compose?text=Looks%20good!&parentCastHash=0x09455067393562d3296bcbc2ec1c2d6bba8ac1f1
```

#### Detalles adicionales

- Los embeds pueden ser cualquier URL válida
- Las URLs que terminan en `.png`, `.jpg` o `.gif` se renderizarán como un embed de imagen
- Al incrustar la URL de una página de mint en Zora, se mostrará el NFT con un enlace de mint debajo.
- Puedes ver cómo se renderizarán los embeds en Warpcast en https://warpcast.com/~/developers/embeds

## URLs de Recursos

#### Ver perfil por FID

```
https://warpcast.com/~/profiles/:fid
```

#### Ver publicación por hash

```
https://warpcast.com/~/conversations/:hash
```
