# Aplicaciones

Para usar Farcaster se requiere una billetera Ethereum para registrar tu cuenta y una interfaz de usuario para navegar por la red. Si eres nuevo, te recomendamos comenzar con Warpcast en [iOS](https://apps.apple.com/us/app/warpcast/id1600555445) o [Android](https://play.google.com/store/apps/details?id=com.farcaster.mobile&hl=en_US&gl=US)

Existen dos tipos de aplicaciones:

1. **Aplicación de Billetera** - permite registrarse, añadir aplicaciones conectadas, publicar y navegar mensajes.
2. **Aplicación Conectada** - solo permite publicar y navegar mensajes.

## Aplicaciones de Billetera

Los usuarios deben instalar una aplicación de billetera para comenzar con Farcaster. Pueden realizar acciones onchain y offchain como registrarse, añadir aplicaciones conectadas, publicar mensajes y seguir usuarios.

Una aplicación de billetera controla la dirección Ethereum que posee la cuenta. Tiene control total sobre la cuenta y puede realizar cualquier acción en tu nombre, así que solo usa una aplicación de billetera en la que confíes.

### Warpcast

Warpcast es una aplicación de billetera desarrollada por el equipo de Farcaster. Tiene una versión web y móvil, aunque el registro solo está disponible en móvil.

- Descargar: [iOS](https://apps.apple.com/us/app/warpcast/id1600555445), [Android](https://play.google.com/store/apps/details?id=com.farcaster.mobile&hl=en_US&gl=US)

## Aplicaciones Conectadas

Las aplicaciones conectadas solo pueden añadirse una vez que el usuario se registra con una aplicación de billetera. Pueden realizar acciones offchain en Farcaster como escribir casts, seguir cuentas y navegar.

Una aplicación conectada controla una clave de aplicación otorgada por la aplicación de billetera. Los usuarios pueden añadir múltiples aplicaciones conectadas a su cuenta y eliminarlas en cualquier momento. Una aplicación conectada maliciosa no puede tomar control de tu cuenta y cualquier acción que realice puede ser revertida por tu aplicación de billetera.

Algunas aplicaciones conectadas populares incluyen:

- [Supercast](https://supercast.xyz/)
- [Yup](https://yup.io/)
- [Farcord](https://farcord.com/)

**Las aplicaciones conectadas no son revisadas por Farcaster, úsalas bajo tu propio riesgo**

## Recursos

### Herramientas

- [Hubble](../../hubble/hubble.md) - un hub de Farcaster para leer y escribir mensajes.
- [Replicator](https://github.com/farcasterxyz/hub-monorepo/tree/main/apps/replicator) - una herramienta para sincronizar un hub con una base de datos postgres.

### Tutoriales

- [Configurar Hubble](../..//hubble/install#install-via-script) - ejecutar un hub de Farcaster.
- [Configurar Replicator](../../developers/guides/apps/replicate) - sincronizar un hub con postgres para facilitar consultas.
- [Esquema para replicación](../../reference/replicator/schema) - esquema para las tablas postgres del replicator.

### Servicios

- [Neynar](https://neynar.com/) - infraestructura y servicios para construir aplicaciones de Farcaster.
