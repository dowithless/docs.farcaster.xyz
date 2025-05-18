# Nombres ENS

Farcaster utiliza nombres ENS como identificadores legibles por humanos para las cuentas. Se admiten dos tipos de nombres ENS:

- **Nombres ENS offchain**: gratuitos y controlados por Farcaster. (ej. @alice)
- **Nombres ENS onchain**: tienen costo y son controlados por tu wallet. (ej. @alice.eth)

Los nombres ENS solo pueden usarse en Farcaster si tienen <= 16 caracteres y contienen solo letras minúsculas, números y guiones.

![Usernames](/assets/usernames.png)

## Nombres ENS Onchain

Los usuarios pueden usar nombres ENS onchain como `@alice.eth` en Farcaster.

Los nombres ENS onchain son emitidos por ENS, terminan en .eth y deben registrarse en la blockchain Ethereum L1. Cualquiera puede registrar un nombre ENS usando la [app de ENS](https://app.ens.domains/).

Los usuarios deben pagar una tarifa para registrar un nombre ENS onchain, pero una vez registrado, es controlado por el usuario y no puede revocarse.

## Nombres ENS Offchain (Fnames)

Los usuarios pueden usar nombres ENS offchain como `@alice` en Farcaster.

Los nombres ENS offchain, también llamados Farcaster Names o Fnames, cumplen con ENS pero se registran offchain. Los Fnames son gratuitos pero están sujetos a una política de uso para prevenir acaparamiento y suplantación. También están sujetos a los siguientes requisitos:

1. Una cuenta solo puede tener un Fname a la vez.
2. Una cuenta puede cambiar su Fname una vez cada 28 días.

### Política de Uso

Registrar un Fname es gratuito pero está sujeto a la siguiente política:

1. Nombres relacionados con figuras públicas o entidades pueden ser reclamados (ej. @google).
2. Nombres que no se han usado por 60+ días pueden ser reclamados.
3. Nombres registrados con el único propósito de reventa pueden ser reclamados.

Las decisiones las toma el equipo de Farcaster y a menudo requieren criterio humano. Usuarios que deseen un nombre completamente bajo su control deben usar un nombre ENS onchain.

### Registro

Los Fnames se emiten como nombres offchain bajo el subdominio `fcast.id`.

Bob puede registrar el nombre ENS offchain `bob.fcast.id` y usarlo en cualquier app de Farcaster con el formato abreviado `@bob`. El nombre puede registrarse haciendo una petición firmada al servidor del Registro de Fnames. Consulta la [referencia de la API de FName](/es/reference/fname/api) para más detalles sobre cómo consultar y crear Fnames.

Para aprender más sobre cómo funcionan los Fnames, consulta [ENSIP-16](https://docs.ens.domains/ens-improvement-proposals/ensip-16-offchain-metadata)
y [ERC-3668](https://eips.ethereum.org/EIPS/eip-3668).
