# Nombres de usuario

Una cuenta de Farcaster necesita un nombre de usuario para que otros usuarios puedan encontrarla y mencionarla. Farcaster utiliza el [Ethereum Name Service](https://ens.domains/) para gestionar los nombres de usuario.

Los nombres ENS son propiedad de direcciones Ethereum, al igual que las cuentas de Farcaster. La diferencia es que una dirección puede poseer múltiples nombres ENS, por lo que la cuenta de Farcaster debe especificar el nombre que desea utilizar. Los nombres ENS solo pueden usarse en Farcaster si tienen <= 16 caracteres y contienen solo letras minúsculas, números y guiones.

## Cambiar nombres de usuario

Una cuenta de Farcaster puede cambiar entre diferentes nombres de usuario en cualquier momento. Cambiar de nombre no afecta tu historial ni a tus seguidores.

Es seguro cambiar tu nombre unas pocas veces al año. Pero cambiar tu nombre con más frecuencia puede hacer que los usuarios o las mini apps pierdan confianza en tu cuenta. Si deseas cambiar un indicador público, considera cambiar tu nombre para mostrar en su lugar.

## Nombres Offchain vs Onchain

Una cuenta puede elegir entre dos tipos de nombres de usuario:

- **Nombres ENS Offchain**: gratuitos y controlados por Farcaster. (ej. @alice)
- **Nombres ENS Onchain**: tienen costo y son controlados por tu wallet. (ej. @alice.eth)

Elige un nombre ENS offchain si deseas comenzar rápidamente y no tienes un nombre ENS onchain. Una cuenta siempre puede actualizar a un nombre onchain más tarde. Se recomienda usar una mini app como Warpcast para configurarlo.

![Nombres de usuario](/assets/usernames.png)

### Nombres ENS Offchain

- Los nombres ENS offchain, también llamados fnames, son gratuitos y emitidos por Farcaster.
- Cualquier cuenta Ethereum puede obtener un fname único llamando al [Fname Registry](/es/learn/architecture/ens-names).
- Los fnames son gratuitos pero pueden ser revocados por Farcaster en cualquier momento.

### Nombres ENS Onchain

- Los nombres ENS onchain, también llamados .eth names, están en cadena y son emitidos por ENS.
- Cualquier cuenta Ethereum puede obtener un ENS llamando al [ENS Registry](https://docs.ens.domains/dapp-developer-guide/the-ens-registry).
- Los nombres no son gratuitos pero no pueden ser revocados por Farcaster.

## Recursos

### Especificaciones

- [Farcaster Name](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md#5-fname-specifications) - Un nombre ENS offchain ENSIP-10 utilizable dentro de Farcaster.
- [UserData: Username](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md#23-user-data) - Establece una Prueba de Nombre de Usuario válida como el nombre de usuario actual.
- [Username Proof](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md#17-username-proof) - Prueba la propiedad de un nombre de usuario onchain u offchain.
- [Verifications](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md#25-verifications) - Prueba la propiedad de una dirección, requerida para Pruebas de Nombre de Usuario onchain.

### APIs

- [UserData API](../../reference/hubble/httpapi/userdata) - Obtener los UserData para el nombre de usuario actual de un usuario.
- [Username Proofs API](../../reference/hubble/httpapi/usernameproof) - Obtener las Pruebas de Nombre de Usuario de un usuario desde un hub.
- [Verification Proofs API](../../reference/hubble/httpapi/verification) - Obtener las Verificaciones de un usuario desde un hub.
- [Fname Registry API](../../reference/fname/api.md) - Registrar y rastrear la propiedad de fnames programáticamente.

### Tutoriales

- [Obtener UserData](../../developers/guides/querying/fetch-profile.md) - Obtener mensajes UserData de una cuenta.
- [Crear UserData](../../developers/guides/writing/messages#user-data) - Crear un mensaje UserData para seleccionar un nombre de usuario válido.
- [Verificar una Dirección](../../developers/guides/writing/verify-address.md) - Verificar la propiedad de una cuenta Ethereum.
- [Encontrar cuenta por nombre de usuario](../../developers/guides/accounts/find-by-name.md) - Encontrar una cuenta por su nombre de usuario.
- [Cambiar nombre de Farcaster](../../developers/guides/accounts/change-fname.md) - Cambiar un nombre de usuario de Farcaster.
