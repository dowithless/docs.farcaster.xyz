---
outline: [2, 3]
---

# Bundler

El Bundler facilita el registro inicial al permitir que un usuario registre un fid, agregue una clave y alquile almacenamiento en una sola transacción.

Si deseas crear una nueva cuenta de Farcaster en una única transacción, utiliza el Bundler.

## Lectura

### price

Obtén el precio en wei para registrar un fid, incluyendo 1 unidad de almacenamiento. Para agregar unidades adicionales de almacenamiento al cálculo, utiliza el parámetro `extraStorage`.

| Parámetro    | tipo      | Descripción                                           |
| ------------ | --------- | ----------------------------------------------------- |
| extraStorage | `uint256` | Número de unidades adicionales a incluir en el precio |

## Escritura

### register

Registra un fid, agrega una o más claves y alquila almacenamiento en un solo paso. Para un ejemplo detallado de uso, consulta la [demo app de registro](https://farcaster-signup-demo.vercel.app/bundler).

| Parámetro      | tipo                 | Descripción                                          |
| -------------- | -------------------- | ---------------------------------------------------- |
| `msg.value`    | `wei`                | Cantidad de pago para el registro                    |
| registerParams | `RegistrationParams` | Parámetros y firma relacionados con el registro      |
| signerParams   | `SignerParams[]`     | Parámetros y firma relacionados con la clave         |
| extraStorage   | `uint256`            | Unidades adicionales de almacenamiento para alquilar |

**Estructura RegistrationParams**

La estructura `RegistrationParams` incluye parámetros de registro y una firma [`Register`](/es/reference/contracts/reference/id-gateway#register-signature) de IdGateway del destinatario del fid.

| Parámetro | tipo      | Descripción                                                                                                  |
| --------- | --------- | ------------------------------------------------------------------------------------------------------------ |
| to        | `address` | Dirección a la que se registrará el fid                                                                      |
| recovery  | `address` | Dirección de recuperación para el nuevo fid                                                                  |
| deadline  | `uint256` | Marca de tiempo de expiración de la firma                                                                    |
| sig       | `bytes`   | Firma EIP-712 [`Register`](/es/reference/contracts/reference/key-gateway#add-signature) de la dirección `to` |

**Estructura SignerParams**

La estructura `SignerParams` incluye parámetros de clave del firmante y una firma [`Add`](/es/reference/contracts/reference/key-gateway#add-signature) de KeyGateway del destinatario del fid. Los llamadores pueden proporcionar múltiples estructuras `SignerParams` para agregar varias claves al momento del registro.

| Parámetro    | tipo      | Descripción                                                                                                                             |
| ------------ | --------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| keyType      | `uint32`  | Debe establecerse en `1`. Actualmente es el único `keyType` soportado.                                                                  |
| key          | `bytes`   | Clave pública a agregar                                                                                                                 |
| metadataType | `uint8`   | Debe establecerse en `1`. Actualmente es el único `metadataType` soportado.                                                             |
| metadata     | `bytes`   | [`SignedKeyRequestMetadata`](/es/reference/contracts/reference/signed-key-request-validator#signedkeyrequestmetadata-struct) codificado |
| deadline     | `uint256` | Marca de tiempo de expiración de la firma                                                                                               |
| sig          | `bytes`   | Firma EIP-712 [`Add`](/es/reference/contracts/reference/key-gateway#add-signature) de la dirección `registrationParams.to`              |

## Errores

| Error            | Selector   | Descripción                                                                                                             |
| ---------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------- |
| InvalidPayment   | `3c6b4b28` | El llamador proporcionó un pago insuficiente.                                                                           |
| InvalidMetadata  | `bcecb64a` | Los metadatos firmados proporcionados con la clave son inválidos.                                                       |
| InvalidSignature | `8baa579f` | La firma proporcionada es inválida. Puede estar mal formateada o firmada por la dirección incorrecta.                   |
| SignatureExpired | `0819bdcd` | La firma proporcionada ha expirado. Obtén una nueva firma del firmante con una marca de tiempo de expiración posterior. |

## Fuente

[`Bundler.sol`](https://github.com/farcasterxyz/contracts/blob/1aceebe916de446f69b98ba1745a42f071785730/src/Bundler.sol)
