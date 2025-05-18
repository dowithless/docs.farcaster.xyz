# Arquitectura

Farcaster tiene una arquitectura híbrida que almacena la identidad en la cadena (onchain) y los datos fuera de la cadena (offchain).

![Arquitectura](/assets/architecture.png)

## Onchain

Los sistemas onchain de Farcaster se implementan como [contratos en OP Mainnet](./contracts.md). Las acciones se realizan onchain solo cuando la seguridad y la consistencia son críticas. El uso de acciones onchain se mantiene al mínimo para reducir costos y mejorar el rendimiento.

Solo unas pocas acciones se realizan onchain, incluyendo:

- Crear una [cuenta](../what-is-farcaster/accounts.md).
- Pagar renta para [almacenar datos](../what-is-farcaster/messages.md#storage).
- Añadir claves de cuenta para [apps conectadas](../what-is-farcaster/apps.md#connected-apps).

## Offchain

El sistema offchain de Farcaster es una red peer-to-peer de servidores llamados [Hubs](./hubs.md) que almacenan datos de usuarios. La mayoría de las acciones de usuario se realizan offchain. Estas incluyen:

- Publicar un nuevo mensaje público.
- Seguir a otro usuario.
- Reaccionar a una publicación.
- Actualizar tu foto de perfil.

Las acciones se realizan offchain cuando el rendimiento y el costo son críticos. El uso de acciones offchain suele preferirse cuando la consistencia no es un requisito estricto. Los sistemas offchain logran seguridad al depender de firmas de los sistemas onchain.
