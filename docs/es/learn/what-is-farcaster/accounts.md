# Cuentas

Una cuenta de Farcaster te permite configurar un nombre de usuario, una foto de perfil y publicar mensajes de texto cortos conocidos como casts. Cualquier dirección de Ethereum puede registrar una cuenta de Farcaster realizando una transacción en la cadena.

## Crear una cuenta

Una cuenta de Farcaster se crea llamando al contrato IdGateway. Este asignará un nuevo Farcaster ID o fid a tu dirección de Ethereum.

Necesitarás obtener un nombre de usuario, alquilar almacenamiento y añadir una clave antes de poder usar tu cuenta. Estos pasos requieren múltiples firmas y transacciones en cadena, lo cual puede ser tedioso con una billetera de Ethereum normal.

Recomendamos comenzar con [Warpcast](https://www.warpcast.com/), un software cliente especial de Farcaster que manejará todo el proceso por ti. También utiliza una cuenta de Ethereum separada para firmar transacciones, por lo que puedes mantener segura tu cuenta principal de Ethereum.

## Añadir claves de cuenta

Las cuentas pueden emitir claves que permiten a las apps escribir mensajes en su nombre. Los usuarios normalmente emiten una clave para cada app de Farcaster que utilizan.

Las claves son gestionadas por el contrato KeyRegistry. Para añadir una clave, necesitarás enviar la clave pública de un par de claves EdDSA junto con una firma del solicitante. El solicitante puede ser la cuenta misma o una app que quiera operar en su nombre.

## Recuperar una cuenta

Los usuarios a menudo configuran billeteras separadas para sus apps sociales y es fácil perder el acceso. Farcaster permite que cualquier cuenta especifique una dirección de recuperación que puede usarse para recuperar la cuenta. Puede configurarse al crear la cuenta o en cualquier momento después.

Los usuarios pueden establecer la dirección de recuperación en servicios confiables como Warpcast o pueden gestionarla ellos mismos usando una billetera de Ethereum normal.

## Recursos

### Especificaciones

- [Especificaciones de Contratos](https://github.com/farcasterxyz/protocol/blob/main/docs/SPECIFICATION.md#1-smart-contracts) - Los contratos en cadena que gestionan cuentas de Farcaster, claves de cuentas y direcciones de recuperación.

### APIs

- [IdRegistry](../../reference/contracts/reference/id-registry) - Buscar datos de cuentas en cadena.
- [IdGateway](../../reference/contracts/reference/id-gateway) - Crear cuentas en cadena.
- [KeyRegistry](../../reference/contracts/reference/key-registry) - Buscar datos de claves de cuentas en cadena.
- [KeyGateway](../../reference/contracts/reference/key-gateway) - Crear claves de cuentas en cadena.
- [Obtener Farcaster IDs](../../reference/hubble/httpapi/fids) - Obtener una lista de todos los fids de cuentas registradas desde un hub.
- [Obtener claves de cuenta](../../reference/hubble/httpapi/onchain#onchainsignersbyfid) - Obtener las claves de cuenta (signers) para una cuenta desde un hub.

### Tutoriales

- [Crear una cuenta](../../developers/guides/accounts/create-account.md) - Crear una nueva cuenta en Farcaster.
- [Crear una clave de cuenta](../../developers/guides/accounts/create-account-key.md) - Crear una nueva clave de cuenta para tu cuenta.
- [Encontrar cuenta por nombre de usuario](../../developers/guides/accounts/find-by-name.md) - Encontrar una cuenta por su nombre de usuario.
- [Cambiar dirección de custodia](../../developers/guides/accounts/change-custody.md) - Cambiar la dirección que posee tu cuenta.
- [Cambiar dirección de recuperación](../../developers/guides/accounts/change-recovery.md) - Cambiar la dirección que recupera tu cuenta.
- [Encontrar solicitante de clave de cuenta](../../developers/guides/advanced/decode-key-metadata.md) - Encontrar la app a la que el usuario concedió una clave de cuenta.
- [Consultar registros desde replicador](../../developers/guides/advanced/query-signups.md) - Consultar el número de registros desde el replicador.
