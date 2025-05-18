# Preguntas Frecuentes (FAQ)

## Firmas

### ¿Cómo genero una firma EIP-712?

Consulta la documentación de referencia de los contratos para obtener información sobre cada firma EIP-712, incluyendo [ejemplos de código](/es/reference/contracts/reference/id-gateway#register-signature) en Typescript.

Si estás usando Typescript/JS, el paquete [`@farcaster/hub-web`](https://www.npmjs.com/package/@farcaster/hub-web) incluye herramientas para generar y trabajar con firmas EIP-712. Para asegurarte de que estás usando las direcciones y typehashes correctos, recomendamos importar las ABIs y tipos EIP-712 desde el [módulo de contratos](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/core/src/eth/contracts) o usar el ayudante [`Eip712Signer`](https://github.com/farcasterxyz/hub-monorepo/blob/main/packages/core/src/signers/eip712Signer.ts) proporcionado.

Revisa la [aplicación de ejemplo](https://github.com/farcasterxyz/hub-monorepo/tree/main/packages/hub-nodejs/examples/contract-signatures) "Trabajando con firmas EIP-712" en el monorepo hub como referencia que demuestra cada firma y llamada a contrato.

### ¿Cómo puedo depurar una firma EIP-712 inválida?

Para ayudar a depurar la firma EIP-712, cada contrato que usa firmas EIP-712 expone su [separador de dominio](https://optimistic.etherscan.io/address/0x00000000fc25870c6ed6b6c7e41fb078b7656f69#readContract#F3) y typehashes como [constantes](https://optimistic.etherscan.io/address/0x00000000fc25870c6ed6b6c7e41fb078b7656f69#readContract#F1) junto con una función de ayuda [hashTypedDataV4](https://optimistic.etherscan.io/address/0x00000000fc25870c6ed6b6c7e41fb078b7656f69#readContract#F6). Si estás construyendo firmas en Solidity u otro lenguaje de bajo nivel, puedes usar esto para ayudar en la depuración.

## Referencia

### ¿Dónde está el código fuente completo de los contratos?

El repositorio de contratos está en Github [aquí](https://github.com/farcasterxyz/contracts).

### ¿Dónde obtengo las ABIs de los contratos?

Encuentra las ABIs de los contratos y direcciones de despliegue [aquí](/es/reference/contracts/deployments#abis).

### ¿Dónde puedo encontrar informes de auditoría?

Los informes de auditorías pasadas están enlazados desde el [repositorio de contratos](https://github.com/farcasterxyz/contracts/blob/1aceebe916de446f69b98ba1745a42f071785730/README.md#audits).

### ¿Están los contratos de Farcaster desplegados en una testnet?

No. Considera usar [network forking](https://book.getfoundry.sh/tutorials/forking-mainnet-with-cast-anvil) para probar o desarrollar contra los contratos de OP mainnet.

## Datos

### ¿Cómo encuentro la dirección de custodia de un usuario?

Llama a la función [`custodyOf`](https://optimistic.etherscan.io/address/0x00000000fc6c5f01fc30151999387bb99a9f489b#readContract#F5) en el [IdRegistry](/es/reference/contracts/reference/id-registry.md).

### ¿Cómo encuentro la dirección de recuperación de un usuario?

Llama a la función [`recoveryOf`](https://optimistic.etherscan.io/address/0x00000000fc6c5f01fc30151999387bb99a9f489b#readContract#F23) en el [IdRegistry](/es/reference/contracts/reference/id-registry.md).

### ¿Cómo encuentro el fid de una cuenta?

Llama a la función [`idOf`](https://optimistic.etherscan.io/address/0x00000000fc6c5f01fc30151999387bb99a9f489b#readContract#F14) en el [IdRegistry](/es/reference/contracts/reference/id-registry.md).

### ¿Cómo busco las claves de cuenta para mi fid?

Llama a la función [`keysOf`](https://optimistic.etherscan.io/address/0x00000000fc1237824fb747abde0ff18990e59b7e#readContract#F16) en el [KeyRegistry](/es/reference/contracts/reference/key-registry.md).

## Otros

### ¿Qué es un app fid? ¿Cómo obtengo uno?

**¿Qué es un FID?**

Un FID (Farcaster ID) es un identificador único usado para distinguir aplicaciones y usuarios. Con un FID, las aplicaciones y usuarios pueden ser identificados y diferenciados.

**¿Por qué es necesario un FID?**

Para crear o publicar cualquier cosa en la plataforma Farcaster, un FID es esencial para identificar tu aplicación o usuario.

**¿Cómo obtengo uno?**

Puedes registrar un app fid directamente a través del [Bundler](/es/reference/contracts/reference/bundler.md) o [IdGateway](/es/reference/contracts/reference/id-gateway.md), o usar un cliente de Farcaster para registrar una cuenta para tu aplicación. Ya que necesitarás firmar [metadatos de solicitud de clave](/es/reference/contracts/reference/signed-key-request-validator.md) desde la wallet que posee tu app fid, mantén la clave privada segura.
